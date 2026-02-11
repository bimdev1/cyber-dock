# Cyber-Dock

> A premium USB-C docking station that is also an autonomous edge server with enterprise-grade remote management.

The Cyber-Dock is not just a hub; it's the ultimate tool for developers, sysadmins, and homelab enthusiasts. It combines a 100W PD workstation dock with a Raspberry Pi CM5-powered management node (PiKVM), 2.5GbE networking, and NVMe storage.

## Key Features

- **Workstation Power:** 100W USB-C Power Delivery (PD) to charge high-end laptops like the Dell XPS 15/17.
- **Autonomous BMC:** Integrated Raspberry Pi CM5 serving as an "iDRAC" for your laptop.
- **Remote KVM:** Low-latency 1080p capture (via CSI) for BIOS-level access over any network.
- **Dual 2.5GbE:** Isolated management and data networks using Realtek PCIe and USB 3.0 controllers.
- **Premium Build:** Internal Mean Well 200W PSU (IEC C14 inlet), CNC aluminum enclosure, and Noctua silent cooling.
- **Storage & Peripherals:** M.2 NVMe NAS, 1.3" OLED status display, front microSD reader, and external WiFi antenna.

## Project Documentation

Detailed research and specifications are available in the [docs/](docs/) directory:

1. [Exploration](docs/exploration.md) - Initial concept and feasibility.
2. [Research](docs/research.md) - Technical architecture and IC selection.
3. [Product Vision](docs/product_vision.md) - Competitive analysis and feature matrix.
4. [Premium Component Spec](docs/premium_spec.md) - Finalized high-end hardware specification.

## Hardware Status

- [x] Phase 1: Conceptual Research
- [x] Phase 2: Premium Component Selection
- [ ] Phase 3: Schematic & PCB Design (KiCad)
- [ ] Phase 4: Enclosure CAD & Thermal Simulation
- [ ] Phase 5: Prototype Manufacturing

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
