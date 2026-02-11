# Premium Component Specification (v2.1)

> **Engineering Status:** Component Selection Locked. Ready for Schematic.

## 1. Power Architecture & Budget

The system is powered by an internal **Mean Well LOP-200-20** (200W).

### Power Budget Breakdown

| Subsystem | Max Power | Note |
| :--- | :--- | :--- |
| **Laptop PD Output** | 100.0 W | Priority load (TPS65987D) |
| **Raspberry Pi CM5** | 15.0 W | Max CPU + PCIe load |
| **NVMe SSD** | 8.0 W | Peak write (Gen 3 x4 drive) |
| **USB Peripherals** | 15.0 W | 2x USB-A (5V/1.5A) + SD |
| **Fan & Logic** | 5.0 W | Fan, OLED, ICs |
| **Efficiency Loss** | 15.0 W | DC-DC conversion heat |
| **TOTAL LOAD** | **~158 W** | **Safe margin within 200W PSU.** |

## 2. Thermal Design Strategy

- **Total Heat Dissipation:** ~25W (Internal logic) + PSU heat.
- **Enclosure:** CNC Aluminum 6061-T6.
  - *Dimensions:* 180 × 100 × 38 mm.
  - *Surface Analysis:* The aluminum shell acts as a massive thermal reservoir.
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

- **M.2 Slot:** M-Key 2280.
- **Lanes:** PCIe Gen 2 x1 (via ASM2806 switch).
- **Bandwidth:** ~450 MB/s real-world. (Limited by CM5 uplink, not the drive).

### Front Panel Interface

- **OLED:** SSD1306 (I2C) - 4-pin header [VCC, GND, SCL, SDA].
- **MicroSD:** Push-push connector (GL3224).
- **Audio:** 3.5mm TRRS Jack (PCM2704).
- **USB:** 1x USB-A 3.0, 1x USB-C (Data only).

## 4. Bill of Materials (Critical ICs)

| Function | Part Number | Manufacturer | Package |
| :--- | :--- | :--- | :--- |
| PD Controller | TPS65987D | Texas Instruments | VQFN-56 |
| USB Hub | VL817-Q7 | VIA Labs | QFN-76 |
| PCIe Switch | ASM2806 | ASMedia | QFN-136 |
| Video Bridge | TC358743XBG | Toshiba | VFBGA-64 |
| Crossbar | PI3USB30532 | Diodes Inc | TQFN-40 |
| HDMI Splitter | LT86102UXE | Lontium | QFN-76 |
