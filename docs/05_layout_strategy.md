# Hardware Layout Strategy & Modularity Plan

> **Goal:** Create a hardware architecture that allows for future upgrades (v1.5, v2.0) while respecting the physics of high-speed signals (PCIe, USB 3.0).

## 1. The Strategy: "Core + Interface" Topology

We will split the design into **Low-Risk Modularity** and **High-Performance Integration**.

### Why NOT full physical modularity?

We are dealing with **PCIe Gen 2/3 (500MB/s+)**, **USB 3.0 (5Gbps)**, and **CSI-2**.

- Every connector adds **insertion loss** and **impedance mismatch**.
- Routing PCIe over headers/ribbon cables requires expensive, specialized connectors and extensive shielding.
- **Decision:** The High-Speed Core MUST remain monolithic for reliability.

---

## 2. Physical Layout Zones

We will organize the **Mainboard** into three distinct isolation zones. This allows "Logical Modularity"—we can redesign one zone (e.g., Power) in v2.0 without re-routing the High-Speed zone.

### Zone A: The High-Speed Core (The "Brains")

*Status: Fixed & Protected*

- **Components:** Raspberry Pi CM5, ASM2806 (PCIe Switch), VL817 (USB Hub), NVMe Slot.
- **Location:** Center of Board.
- **Routing Strategy:** Shortest possible traces. Controlled impedance (85Ω/90Ω/100Ω).
- **Upgrade Path:** The CM5 itself is the upgrade. The carrier board supports any CM5-compatible module.

### Zone B: The Power Stage (The "Engine")

*Status: Modular Block*

- **Components:** USB-C Input, TPS65987D (PD Controller), 20V/5V/3V3 DC-DC Buck Converters.
- **Location:** Rear/Side edge.
- **Modularity:** This section controls the input power.
- **v2.0 Path:** In the future, this entire layout block can be ripped up and replaced with a **POE++** or **Thunderbolt 4** power stage, assuming the output rails (20V, 5V, 3V3) remain the same.

### Zone C: The User Interface (The "Face")

*Status: Physically Modular (Daughterboard)*

- **Components:** OLED Display, Status LEDs, Power Button, Force-Off Relay Trigger.
- **Strategy:** **Move these to a separate, passive PCB.**
- **Connection:** Low-speed FFC (Flat Flex Cable) or Pin Header to the Mainboard.
- **Benefit:** Allows complete redesign of the enclosure's front panel (e.g., moving the screen, changing button styles) without forcing an expensive Mainboard re-spin.

---

## 3. KiCad Project Structure (Hierarchical Design)

We will use **Hierarchical Sheets** in KiCad. This is the software equivalent of modular code.

```
Root.kicad_sch
├── Power_Delivery.kicad_sch       (TPS65987D + Regulators)
├── CM5_Integration.kicad_sch      (Connectors + Fan Control)
├── High_Speed_IO.kicad_sch        (PCIe Switch + USB Hub + NVMe)
├── Video_Path.kicad_sch           (CSI Bridge + HDMI Splitter)
└── Front_Panel_Connector.kicad_sch (Interface to Daughterboard)
```

**Why this helps:**
If we build the "SD Card Reader" later, we just add a `Storage_Expansion.kicad_sch` sheet and hook it into the bus, keeping the other sheets clean.

## 4. Execution Plan adjustment

1. **Define Pinout Matrix (Updated):** Map GPIOs not just to "OLED" but to "Front Panel Header".
2. **Schematic Entry:** Create the specific Hierarchical Sheets listed above.
3. **Layout:** Route the High-Speed Core first, then build the Power and Interface zones around it.

## 5. Summary of "Modular" Decisions

| Feature | Modularity Approach |
| :--- | :--- |
| **Compute** | **Physical:** Replaceable CM5 Module. |
| **Front Panel/UI** | **Physical:** Separate Daughterboard (FFC). |
| **Power Input** | **Logical:** Isolated schematic block (easy v2.0 re-design). |
| **High-Speed data** | **Monolithic:** Hard-wired for Signal Integrity. |
