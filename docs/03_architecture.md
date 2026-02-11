# Research: Cyber-Dock Technical Architecture

> **Objective:** Design a seamless integration of a high-performance USB-C dock and a Raspberry Pi CM5.

## 1. Core Architecture

The system is built around three main data buses connecting the Laptop and the CM5:

1. **USB-C (Data & Power):** The primary umbilical to the laptop. Carries USB 3.0 data, DP Alt Mode video, and 100W power.
2. **PCIe Gen 2 x1 (CM5):** The high-speed expansion lane for the CM5, used for NVMe storage and Management Ethernet.
3. **CSI-2 (Video Capture):** A direct video bridge to capture the laptop's HDMI output for the KVM function.

## 2. Component Selection & Justification

### A. Power Delivery & Management

- **Component:** **TI TPS2595** (eFuse / High-Side Switch) on the 20V rail.
- **Control:** CM5 GPIO 6 (Active High) → TPS2595 `EN` pin.
- **Status:** TPS2595 `FLT_N` → CM5 (Fault/State feedback).
- **Use Case:** Recovering from frozen OS states where software reboot fails.

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
