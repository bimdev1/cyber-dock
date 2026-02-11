# Power States & Sequencing (v1.0)

> **Objective:** Ensure safe, monotonic startup of the Cyber-Dock, protecting voltage-sensitive rails on the CM5 and high-speed ICs.
> **Critical:** CM5 must be held in reset until all rails (5V, 3V3) are stable.

## 1. Voltage Regulator Topology

- **Input:** 20V DC (from Internal Mean Well PSU).
- **Primary Rails:**
  1. **5V_SYS** (Buck, 5A): Power for USB Hub, CM5 Input, Peripherals.
  2. **3V3_SYS** (Buck, 3A): Logic power for Switch, Hub, Interface.
  3. **1V05 / 1V8** (LDOs): Local regulation for ASM2806 / VL817 (If not internal).
- **Load Switches:**
  1. **20V_VBUS_OUT** (TPS2595): Gated power to Laptop.
  2. **3V3_PCIE** (Load Switch): Gated power to NVMe/NIC (Controlled by CM5).

## 2. Power-Up Sequence (Cold Boot)

This sequence is enforced by **Hardware (PG signals)** and **RC Delays**, not software.

| Time (T) | Rail/Event | Description | Condition |
| :--- | :--- | :--- | :--- |
| **0ms** | `20V_IN` | PSU turns on. | Input stable. |
| **+5ms** | `5V_SYS` | 20V->5V Buck starts. | 20V > UVLO. |
| **+10ms** | `3V3_SYS` | 5V->3V3 Buck starts. | `5V_PG` + 5ms delay. |
| **+20ms** | `VSYS_CM5` | CM5 5V Input stable. | `5V_SYS` stable. |
| **+50ms** | `GLOBAL_RESET` | CM5 Reset De-asserted. | `3V3_PG` is High. |
| **+2s** | `20V_VBUS` | Laptop Power Logic. | CM5 boots, negotiates PD. |
| **+3s** | `3V3_PCIE` | Peripherals enabled. | CM5 Software enables payload. |

## 3. Power-Down Sequence (Safe Shutdown)

1. **Software Command:** User requests Shutdown (or UPS triggers it).
2. **Payload Off:** CM5 cuts `3V3_PCIE` (Protect NVMe data).
3. **VBUS Off:** CM5 disables `20V_VBUS` (Laptop relies on battery).
4. **Halt:** CM5 shuts down OS.
5. **Hard Cut:** Global power removal (External Switch or AC Cut).

## 4. Protection Mechanisms

- **TPS2595 (Force Off):**
  - High-side eFuse on `20V_VBUS`.
  - Configured Limit: **5.0A** (100W).
  - reaction Time: < 2us (Short circuit).
- **3V3 Monitor:**
  - If `3V3_SYS` drops below 3.0V, `GLOBAL_RESET` is asserted immediately to prevent CM5 brownout corruption.
