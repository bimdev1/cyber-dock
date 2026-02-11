# Exploration: Cyber-Dock Concept

## Single-Cable Dream
A docking station that handles not only power and peripherals but also serves as an autonomous **out-of-band management** server (PiKVM) and **edge compute** node (Docker/NAS).

## Core Requirements
- **100W Power Delivery (PD):** Must charge a workstation laptop (e.g., Dell XPS, MacBook Pro).
- **PiKVM Integration:** Display capture from laptop, HID emulation (keyboard/mouse), and power control.
- **Ethernet Hub:** Reliable networking for both laptop and dock (SBC).
- **Storage:** NVMe SSD for fast local NAS and PiKVM ISO library.

## Feasibility
- **CM4/CM5:** Perfect base. CM5 preferred for performance.
- **CSI Capture:** Low-latency video capture via TC358743 bridge.
- **USB Topology:** Needs a high-quality USB 3.0 hub (e.g., VL817) and a PD controller (e.g., TPS65987D).
