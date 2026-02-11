# Research: Cyber-Dock Technical Architecture

> **Objective:** Design a seamless integration of a high-performance USB-C dock and a Raspberry Pi CM5.

## 1. Core Architecture

The system is built around three main data buses connecting the Laptop and the CM5:

1. **USB-C (Data & Power):** The primary umbilical to the laptop. Carries USB 3.0 data, DP Alt Mode video, and 100W power.
2. **PCIe Gen 2 x1 (CM5):** The high-speed expansion lane for the CM5, used for NVMe storage and Management Ethernet.
3. **CSI-2 (Video Capture):** A direct video bridge to capture the laptop's HDMI output for the KVM function.

## 2. Component Selection & Justification

### A. Power Delivery & Management

- **Primary PSU:** **Mean Well LOP-200-20** (Internal Open Frame).
  - *Spec:* 20V DC / 10A (200W).
  - *Why:* Eliminates external brick. Medical-grade safety. 200W covers laptop (100W) + system overhead.
- **PD Controller:** **TI TPS65987D**.
  - *Role:* Negotiates PD contracts. Controls power FETs. Handles DP Alt Mode muxing.
  - *Why:* Industry standard, fully configurable via I2C or external flash. "Wait-less" role swapping.

### B. USB Topology (VL817 Hub)

- **Hub Controller:** **Via Labs VL817-Q7** (USB 3.1 Gen 1).
  - *Upstream:* Laptop.
  - *Downstream Ports:*
    1. **RTL8156B** (2.5GbE for Laptop).
    2. **USB-A Front** (Peripheral).
    3. **USB-A Rear** (Peripheral).
    4. **Unused** (Reserved for future).

### C. PCIe Interconnect (ASM2806)

- **Switch:** **ASMedia ASM2806** (PCIe Gen 3 Packet Switch).
  - *Input:* 1 lane (from CM5).
  - *Output:* 2+ lanes.
    1. **NVMe SSD** (Slot M.2 M-key).
    2. **RTL8125BG** (2.5GbE Management NIC).
  - *Why:* CM5 only has 1 PCIe lane. We need simultaneous fast storage and fast networking.

### D. System Control & GPIO

- **Force Power Off Circuit:**
  - **Function:** Physically cuts VBUS power to the laptop to force a reboot.
  - **Mechanism:** High-side PCie-controlled Load Switch or MOSFET on the 20V rail.
  - **Control:** CM5 GPIO (Active High) → Gate Driver → FET.
  - **Use Case:** Recovering from frozen OS states where software reboot fails.

### D. KVM Video Path (The "Loopback")

1. **Laptop sends video** via USB-C DP Alt Mode.
2. **Crossbar Switch (PI3USB30532):** Separates USB 3.0 data from DisplayPort traces.
3. **DP-to-HDMI (PS8625):** Converts DisplayPort signal to HDMI.
4. **HDMI Splitter (LT86102UXE):** Duplicates the HDMI signal.
   - Path A: External HDMI port (Monitor).
   - Path B: **Toshiba TC358743** (HDMI-to-CSI Bridge) → CM5.

## 3. Signal Integrity & PCB Considerations

- **Impedance Control:** USB 3.0 (90Ω differential), PCIe (85Ω differential), HDMI (100Ω differential).
- **Stackup:** 6-layer minimum required for proper routing and power planes.
  - L1: Signal (High Speed)
  - L2: Ground (Solid Reference)
  - L3: Power (20V, 5V, 3.3V distributed)
  - L4: Signal (Low Speed / GPIO)
  - L5: Ground
  - L6: Signal (Bottom components)
- **Thermal Vias:** Heavy stitching under the CM5, ASM2806, and Power FETs to dump heat into the aluminum case.

## 4. Software Stack

- **OS:** Custom PiKVM OS build (Arch Linux ARM base).
- **Drivers:**
  - `tc358743` (V4L2 video capture).
  - `dwc2` (USB OTG gadget mode for HID/MSD).
  - `r8152` / `r8169` (Realtek Ethernet).
- **Services:**
  - `kvmd` (KVM daemon).
  - `dockerd` (Container host).
  - `tailscaled` (VPN mesh).
