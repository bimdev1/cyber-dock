# Power States & Sequencing (v1.0)

> **Objective:** Ensure safe, monotonic startup of the Cyber-Dock, protecting voltage-sensitive rails on the CM5 and high-speed ICs.
> **Critical:** CM5 must be held in reset until all rails (5V, 3V3) are stable.

## 1. Power Tree Topology

```mermaid
graph TD
    DC_IN[20V DC Input] -->|Fuse/Filter| VBUS_20V[20V Rail]
    VBUS_20V -->|TPS2595 eFuse| LAPTOP[Laptop VBUS]
    VBUS_20V -->|TPS54560 Buck| SYS_5V[5.0V System]
    
    SYS_5V -->|Internal PMIC| CM5_CORE[CM5 Core Rails]
    SYS_5V -->|TPS54332 Buck| SYS_3V3[3.3V IO / PCIe]
    SYS_3V3 -->|LDO| SYS_2V5[2.5V Analog (ASM2806)]
    SYS_3V3 -->|LDO| SYS_1V1[1.1V Core (VL817/Switch)]
```

## 2. Startup Sequence (Hardware Enforced)

> **Goal:** Ensure stable rails before the CM5 attempts to boot or train PCIe.

1. **T+0ms (Input):** 20V Applied -> **TPS54560** Soft Starts `5V_SYS`.
2. **T+10ms:** `5V_SYS` reaches stable region -> Enables **TPS54332** (3V3 Buck).
3. **T+20ms:** `3V3_SYS` reaches stable region.
4. **T+22ms:** LDOs (2.5V, 1.1V) stabilize (fast start).
5. **T+25ms:** **TPS54332 Power Good (PG)** signal releases `GLOBAL_RESET`.
    * *Mechanism:* PG is Open-Drain with Pull-Up. Held Low until Vout > 94%.
6. **T+30ms:** CM5 Internal PMIC starts; ROM Boots.

## 3. Voltage Monitoring & Reset

| Rail | Monitor Source | Action on Fault |
| :--- | :--- | :--- |
| **20V** | TPS2595 (`FLT_N`) | Interrupt CM5 (GPIO 26); Status LED. |
| **5.0V** | TPS54560 (Internal) | Cycle Shutdown / Hiccup Mode. |
| **3.3V** | **TPS54332 (PG)** | Pull `GLOBAL_RESET` Low -> Hard Reset CM5. |
| **LDOs** | None (Passive) | Reliance on 3V3 input stability. |

## 4. Laptop Power State (VBUS)

* **Default:** OFF (Safe State).
* **Enable Condition:** CM5 Booted + Software Check Pass.
* **Action:** GPIO 6 High -> **TPS2595** Enable.
* **Soft-Start:** dV/dt cap set for < 5A inrush current.
* **Reasoning:** Security Air Gap. Laptop powers on only when "Dock" is verified safe.

## 3. Power-Down Sequence (Safe Shutdown)

1. **Software Command:** User requests Shutdown (or UPS triggers it).
2. **Payload Off:** CM5 cuts `3V3_PCIE` (Protect NVMe data).
3. **VBUS Off:** CM5 disables `20V_VBUS` (Laptop relies on battery).
4. **Halt:** CM5 shuts down OS.
5. **Hard Cut:** Global power removal (External Switch or AC Cut).

## 4. Protection Mechanisms

* **TPS2595 (Force Off):**
  * High-side eFuse on `20V_VBUS`.
  * Configured Limit: **5.0A** (100W).
  * reaction Time: < 2us (Short circuit).
* **3V3 Monitor:**
  * If `3V3_SYS` drops below 3.0V, `GLOBAL_RESET` is asserted immediately to prevent CM5 brownout corruption.
