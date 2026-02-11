# Hardware Validation Plan (v1.0)

> **Status:** DRAFT
> **Target:** Guide for Schematic Entry, Layout, and Prototype Bring-up.

## 1. Decoupling Strategy

> **Rule of Thumb:** "Smallest capacitance closest to the pin."

### A. Power Regulators (Source)

* **Input (20V):** 2x 10µF (50V, X7R, 1210) + 1x 0.1µF (0402) at TPS54560 input.
* **Output (5V):** 2x 22µF (10V, X7R, 1206) + 1x 100µF (Polymer/Tantalum) for bulk stability.
* **Output (3.3V):** 2x 22µF (6.3V, X7R, 0805).

### B. High-Speed Digital ICs (VL817, ASM2806, RTL8125BG, PS8625)

* **Local Bypass:** One **0.1µF (0402)** capacitor per VDD pin, placed < 2mm from the pin.
* **Rail Bulk:** One **10µF (0603)** capacitor per power rail group/corner of the IC.
* **Crystal:** 18pF-22pF (NP0) loads for crystals, routed differentially with ground guard ring.

### C. Sensitive Analog / PLL (TC358743, CM5 AVDD)

* **Noise Isolation:** Ferrite Bead (e.g., BLM18HE152) in series with the supply.
* **Filtering:** 10µF (Bulk) + 0.1µF + 1nF (HF) on the device side of the bead.
* **Critical Rails:** `MIPIRX_VDD`, `CSI_VDD`.

### D. CM5 Module / Daughterboard

* **CM5 5V Input:** 2x 47µF (6.3V, 1210) near the B2B connectors to handle load transients.
* **FFC Connector:** 1x 1µF on 3V3 lines to dampen mating transients.

---

## 2. Test Point Plan

> **Placement:** 1mm circular test pads on bottom layer, labeled on silkscreen.

### A. Power Rails (Voltage & Noise)

* `TP_20V_IN` (Main Input)
* `TP_5V_SYS` (System Primary)
* `TP_3V3_SYS` (IO/PCIe)
* `TP_1V8_CORE` (If external LDO used)
* `TP_1V05_CORE` (If external LDO used)
* `TP_GND_REF` (Near power staging area)

### B. Control Signals (Logic)

* `TP_GLOBAL_RST` (CM5 Reset Line)
* `TP_PG_3V3` (Power Good)
* `TP_RELAY_EN` (Force Power Off Gate)
* `TP_RELAY_FLT` (Switch Fault)

### C. Debug Interfaces

* `TP_UART_TX` / `TP_UART_RX` (Console)
* `TP_I2C_SDA` / `TP_I2C_SCL` (OLED/System Control)
* `TP_USB_P` / `TP_USB_N` (USB 2.0 Uplink probe points)

---

## 3. Bring-up Sequence (Prototype v1)

### Phase 1: Passive Checks (No Power)

1. **Visual Inspection:** Check for tombstoned caps, solder bridges (especially QFNs), and connector alignment.
2. **Resistance Check:** Measure resistance from each Power Rail to GND.
    * *Pass Criteria:* 20V > 100kΩ, 5V > 10kΩ, 3V3 > 1kΩ. (Low resistance suggests short).

### Phase 2: Power Regulation (No CM5)

1. **Apply 20V:** Limit supply current to 0.5A.
2. **Check 5V_SYS:** Verify 5.0V ±5%. Check ripple.
3. **Check 3V3_SYS:** Verify 3.3V ±5%.
4. **Verify PG Logic:** Ensure `PG_3V3` is HIGH and `GLOBAL_RESET` is HIGH (since CM5 not pulling it down).
5. **Check LED_PWR:** Should be illuminated.

### Phase 3: System Boot (With CM5)

1. **Install CM5.**
2. **Connect UART Console.**
3. **Power On:** Observe current jump (~300mA idle).
4. **Console Output:** Watch for Boot ROM -> Linux Kernel log.
5. **Verify I2C:** Run `i2cdetect -y 1` (Expect OLED, TPS65987D if connected).

### Phase 4: High-Speed Peripheral Validation

1. **PCIe Enumeration:** `lspci -nn`
    * *Expect:* ASM2806 Switch -> NVMe Controller + RTL8125BG.
2. **USB Hub:** `lsusb -t`
    * *Expect:* VL817 Hub hierarchy.
3. **Video Capture:** `v4l2-ctl --list-devices`
    * *Expect:* TC358743 (Toshiba Bridge).
4. **Network:** `ip link show`
    * *Expect:* `eth0` (CM5 Internal) + `eth1` (RTL8125BG).

### Phase 5: Power Delivery & Loopback

1. **Connect Laptop (USB-C).**
2. **PD Contract:** Verify 20V negotiation via TPS65987D status (I2C/GPIO).
3. **Video Loopback:** Verify external monitor output (HDMI) and Capture stream availability.
