# Premium Component Specification (v2.1)

> **Engineering Status:** Component Selection Locked. Ready for Schematic.

## 1. Power Architecture & Budget

The system is powered by an internal **Mean Well LOP-200-20** (200W).

### Power Budget Breakdown

| Subsystem | Max Power | Note |
| :--- | :--- | :--- |
| **Laptop PD Output** | 100.0 W | Priority load (TPS65987D) |
| **Raspberry Pi CM5** | 15.0 W | Max CPU + PCIe load |
| **NVMe SSD** | 8.0 W | Peak write (Gen 3 x4 drive, Gen 2 speed) |
| **USB Peripherals** | 10.0 W | 2x USB-A (5V/1.0A) - No SD/Audio |
| **Fan & Logic** | 5.0 W | Fan, OLED, ICs |
| **regulator Losses** | 5.0 W | ~90% efficient Buck Converters (No LDO heat) |
| **TOTAL LOAD** | **~143 W** | **Safe margin within 200W PSU.** |

## 2. Thermal Design Strategy

- **Total Heat Dissipation:** ~25W (Internal logic) + PSU heat.
- **Enclosure:** CNC Aluminum 6061-T6.
  - *Dimensions:* 180 × 100 × 38 mm.
  - **Surface Analysis:** The aluminum shell acts as a massive thermal reservoir.
- **Active Cooling:** **Noctua NF-A4x10 5V PWM**.
  - *Placement:* Internal, blowing across the Main logic PCB and CM5 heatsink.
  - *Airflow Path:* Intake vents (bottom/side) → PCB/CPU → Exhaust vents (rear).
  - *Control:* PWM governed by CM5 CPU temperature.
    - < 45°C: 20% RPM (Silent)
    - > 65°C: 100% RPM

## 3. High-Speed IO & Connectors

### Networking

1. **Management LAN (CM5):**
    - **IC:** Realtek RTL8125BG-CG (PCIe).
    - **Speed:** 2.5 Gbps.
    - **Connector:** RJ45 w/ Magnetics (MagJack).
2. **Laptop Data LAN:**
    - **IC:** Realtek RTL8156B-CG (USB 3.0).
    - **Speed:** 2.5 Gbps.
    - **Connector:** RJ45 w/ Magnetics.

### Storage

- M.2 NVMe (PCIe Gen 3 x4 physical, Gen 2 x1 electrical via ASM2806)

## 4. Critical ICs & Active Components

| Component | Category | Role | Package |
| :--- | :--- | :--- | :--- |
| **Mean Well LOP-200-20** | Power | Internal 200W PSU (20V/10A). | 4"x2" Open Frame |
| **TI TPS65987D** | Power | USB-C PD Controller. | QFN-56 |
| **TI TPS2595** | Power | 20V eFuse / Verification / Safety. | QFN-10 |
| **TI TPS54560** | Power | 5V 5A Step-Down Converter. | SO-8 PowerPad |
| **TI TPS54332** | Power | 3.3V 3.5A Step-Down Converter. | SO-8 PowerPad |
| **TLV70025** | Power | 2.5V LDO (for ASM2806). | SOT-23 |
| **TLV62569** | Power | 1.1V 2A Buck Converter (High Efficiency). | SOT-563 |
| **TPS22965** | Power | 3.3V Load Switch (PCIe Power Control). | WSON-8 |
| **CM5** | Compute | Main System Controller. | B2B Connectors |
| **VL817-Q7** | USB Hub | USB 3.1 Gen 1 Hub. | QFN-76 |
| **ASM2806** | PCIe | PCIe Gen 3 Switch (Packet Switch). | QFN-136 |
| **RTL8125BG** | Network | 2.5GbE Controller (PCIe). | QFN-48 |
| **PI3USB30532** | Mux | USB/DP Crossbar Switch. | TQFN-40 |
| **LT86102UXE** | Splitter | HDMI 1.4 Splitter (1-in-2-out). | QFN-76 |
| **PS8625** | Converter | DP to HDMI 1.4b Converter. | QFN-48 |
| **TC358743** | Bridge | HDMI to CSI-2 Bridge. | BGA-64 |
