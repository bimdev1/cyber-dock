# Hardware Pinout Matrix (v1.0)

> **Status:** DRAFT
> **Target:** IO Planning for Schematic Entry

## 1. High-Speed Interfaces

| Signal Group | CM5 Pin(s) | Destination IC | Net Name | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **PCIe Gen 2** | 100, 102 (TX) / 106, 108 (RX) / 104, 110 (CLK) | ASM2806 (Switch) | `PCIE_TX_P/N`, `PCIE_RX_P/N` | AC coupling caps required on TX lines. |
| **HDMI 0** | 44, 46, 50, 52, 56, 58, 62, 64 | N/A (Unused) | `HDMI0_...` | We use DSI/CSI, not HDMI out. |
| **HDMI 1** | - | N/A (Unused) | `HDMI1_...` | |
| **CSI-2 (Cam 1)** | 4, 6 (D0) / 10, 12 (D1) / 82, 84 (CLK) | TC358743 (Bridge) | `CSI1_D0_P/N`, `CSI1_CLK_P/N` | 2-lane configuration. |
| **CSI-2 (Cam 0)** | - | N/A | - | Unused. |
| **DSI (Disp 1)** | - | N/A | - | Unused. |
| **USB 2.0** | 105, 107 | VL817 (Hub) | `USB_D_P/N` | Hub uplink. |

## 2. Low-Speed IO & Control

| GPIO | Function | Destination | Pull | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **GPIO 0** | ID_SD | - | - | Reserved for HAT ID. |
| **GPIO 1** | ID_SC | - | - | Reserved for HAT ID. |
| **GPIO 4** | FAN_PWM | Noctua Fan | - | System Fan Control. |
| **GPIO 5** | FAN_TACH | Noctua Fan | Up | System Fan RPM. |
| **GPIO 42** | `ETH_RST` | RTL8125BG | Down | Core: PCIe Ethernet Reset. |
| **GPIO 44** | `SWITCH_RST` | ASM2806 | Down | Core: PCIe Switch Reset. |

## 3. Power Management Signals (Core)

| Signal | Source | Destination | Setup | Notes |
| :--- | :--- | :--- | :--- | :--- |
| `GLOBAL_EN` | CM5 (PMIC_EN) | 3V3/1V8 Regulators | - | Main enable chain. |
| `PG_3V3` | 3V3 Buck | CM5 (GLOBAL_RESET) | - | Hold CM5 in reset until power is stable. |

## 4. Front Panel Interface (Daughterboard Connector)

> **Connection:** 20-pin FFC (Flat Flex Cable).
> **Signals:** I2C, UART, Power Control, Status LEDs.

| Pin | Signal Name | CM5/Source | Function |
| :--- | :--- | :--- | :--- |
| 1 | `3V3_STBY` | 3V3 Reg | Always-on power for Power Button LED. |
| 2 | `GND` | GND | - |
| 3 | `PWR_BTN_N` | PMIC_WAKE | Power On/Off Request (Active Low). |
| 4 | `RELAY_EN` | GPIO 6 | **Force Power Off** Trigger (Active High). |
| 5 | `I2C1_SDA` | GPIO 2 | OLED Display Data. |
| 6 | `I2C1_SCL` | GPIO 3 | OLED Display Clock. |
| 7 | `UART0_TX` | GPIO 14 | Serial Console TX. |
| 8 | `UART0_RX` | GPIO 15 | Serial Console RX. |
| 9 | `LED_ACT` | ACT_LED | Activity LED. |
| 10 | `LED_PWR` | PWR_LED | Power Status LED. |
| ... | ... | ... | ... |
