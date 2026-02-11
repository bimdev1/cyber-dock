# Research: Cyber-Dock Technical Architecture

## IC Selection
- **USB Hub:** VL817 (4-port USB 3.1 Gen 1).
- **PD Controller:** TPS65987D (100W Source/Sink, DP Alt Mode support).
- **Video Capture:** TC358743 (HDMI to CSI-2 bridge).
- **USB-A Controllers:** RTS5411 or similar high-integration hub ICs.
- **SBC:** Raspberry Pi CM5 (due to PiKVM community support and CSI interface).

## Signal Path
1. **Laptop USB-C** → CC lines handled by TPS65987D.
2. **DP Alt Mode** signals routed via PI3USB30532 crossbar switch.
3. **USB 3.0 data** to VL817 hub.
4. **HDMI out** split to external monitor and TC358743 capture bridge.

## Thermal Management
- Aluminum enclosure required as primary heatsink.
- Active cooling (Noctua 5V PWM fan) via GPIO control.
