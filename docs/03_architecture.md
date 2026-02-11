# Cyber-Dock Technical Architecture (v2.0)

> **Objective:** Design a seamless integration of a high-performance USB-C dock and a Raspberry Pi CM5.
> **Philosophy:** The "Dock" and the "Server" are chemically bonded. The CM5 is not just an add-on; it manages the power, negotiates the USB-C connection, and acts as the gatekeeper for the laptop.

## 1. Core Architecture

The system is built around three main data buses connecting the Laptop and the CM5:

1. **USB-C (Data & Power):** The primary umbilical to the laptop. Carries USB 3.0 data, DP Alt Mode video, and 100W power.
2. **PCIe Gen 2 x1 (CM5):** The high-speed expansion lane for the CM5, used for NVMe storage and Management Ethernet.
3. **CSI-2 (Video Capture):** A direct video bridge to capture the laptop's HDMI output for the KVM function.

---

## 2. Signal Path Topology

### A. USB 3.0 & Data Path (The "Hub")

**Goal:** Allow the Laptop to see Peripherals + CM5 (as a Device).

```mermaid
graph LR
    LAPTOP[Laptop USB-C] -->|USB3 + DP| MUX[PI3USB30532 Mux]
    MUX -->|USB3| HUB[VL817-Q7 Hub]
    
    subgraph "VL817 Hub Downstream"
        HUB -->|Port 1| ETH_LAPTOP[RTL8156B 2.5GbE]
        HUB -->|Port 2| USB_A_1[Front USB-A]
        HUB -->|Port 3| USB_A_2[Front USB-A]
        HUB -->|Port 4| CM5_OTG[CM5 USB 2.0 (Gadget Mode)]
    end
```

* **Key Component:** **Pericom PI3USB30532**.
  * **Function:** Splits the USB-C connector's High-Speed pairs into USB 3.0 Data and DisplayPort lanes based on PD negotiation (CC pins).
* **Key Component:** **Via Labs VL817**.
  * **Function:** 4-Port USB 3.1 Gen 1 Hub.
  * **OTG Wiring:** Port 4 is hardwired to the CM5's USB 2.0 OTG pins.
  * **Data Path:** `Laptop` <-> `VL817 Upstream` <-> `VL817 Port 4` <-> `CM5`. The CM5 enumerates as a USB device (HID/Mass Storage) to the laptop through this hub chain.

### B. PCIe Interconnect (The "Server")

**Goal:** Maximize the single PCIe Gen 2 x1 lane from the CM5.

```mermaid
graph TD
    CM5[Raspberry Pi CM5] -->|PCIe Gen 2 x1| SWITCH[ASMedia ASM2806 Switch]
    
    subgraph "ASM2806 Packet Switch"
        SWITCH -->|Lane 0| NVME[M.2 NVMe SSD (Gen 3 x4 slot)]
        SWITCH -->|Lane 1| ETH_MGMT[RTL8125BG 2.5GbE]
        SWITCH -->|Lane 2| NC[Unused/Expansion]
        SWITCH -->|Lane 3| NC[Unused/Expansion]
    end
```

* **Bandwidth Constraint:** The CM5 link is Gen 2 x1 (~500 MB/s total).
* **Contention:** The NVMe drive and the 2.5GbE NIC share this bandwidth.
  * *Real-world:* 500 MB/s is plenty for "Rescue ISO" reads and standard NAS usage. It will bottleneck heavy concurrent 2.5Gbps network transfers + disk writes, but this is acceptable for an edge management device.
* **NVMe Spec:** Slot is Physical Gen 3 x4 (M-Key), but Electrical Gen 2 x1.

### C. Video Path (The "KVM")

**Goal:** Capture Laptop video (KVM) AND output to External Monitor (Dock) simultaneously.

```mermaid
graph LR
    LAPTOP -->|DP Alt Mode| MUX[PI3USB30532]
    MUX -->|DisplayPort| CONV[Parade PS8625]
    CONV -->|HDMI 1.4b| SPLIT[Lontium LT86102UXE]
    
    subgraph "Video Splitter"
        SPLIT -->|Out A| HDMI_EXT[External Monitor]
        SPLIT -->|Out B| BRIDGE[Toshiba TC358743]
    end
    
    BRIDGE -->|CSI-2 (2-Lane)| CM5[Raspberry Pi CM5]
```

* **The "Headless" Problem:** If no monitor is plugged in, Laptops usually turn off the display.
* **The Solution:** The **LT86102UXE** (or PS8625) must handle **EDID Emulation**. It must report a "Generic 1080p Monitor" to the laptop at all times so the video signal is active for the KVM capture.

---

## 3. Power Architecture

### A. Power Delivery (Source & Sink)

* **PSU:** Mean Well LOP-200-20 (200W Internal).
* **Controller:** **TI TPS65987D**.
  * Manages USB-C PD 3.0 negotiation.
  * Controls the Power FETS (Back-to-back N-MOS).
  * Communicates with CM5 via I2C to report charging voltage/current.

### B. Safety & "Air Gap"

* **Component:** **TI TPS2595** (eFuse).
* **Role:** The "Kill Switch".
* **Logic:** The CM5 controls the `EN` pin of this eFuse.
  * `GPIO 6 HIGH` = Laptop has Power.
  * `GPIO 6 LOW` = Laptop is physically cut off.
  * **Hardware Override:** The front-panel "Force Off" button is wired in parallel to pull this EN pin low immediately, bypassing the CM5 software.

---

## 4. Software Stack support

* **OS:** Custom PiKVM OS build (Arch Linux ARM base).
* **Drivers:**
  * `tc358743`: V4L2 video capture driver (Mainline Linux).
  * `dwc2`: USB OTG gadget subsystem (for HID/MSD emulation).
  * `r8152`: Driver for the USB-side Ethernet (Laptop link).
  * `r8169`: Driver for the PCIe-side Ethernet (Management link).
* **Services:**
  * `kvmd`: The PiKVM daemon handling video encoding (uStreamer/Janus) and HID events.
  * `dockerd`: Hosting local containers.
  * `tailscaled`: Providing secure overlay network access.
