# Cyber-Dock

> A premium USB-C docking station that is also an autonomous edge server with enterprise-grade remote management.

The Cyber-Dock is not just a hub; it's the ultimate tool for developers, sysadmins, and homelab enthusiasts. It combines a 100W PD workstation dock with a Raspberry Pi CM5-powered management node (PiKVM), 2.5GbE networking, and NVMe storage.

## Key Features

- **Workstation Power:** 100W USB-C Power Delivery (PD) to charge high-end laptops like the Dell XPS 15/17.
- **Autonomous BMC:** Integrated Raspberry Pi CM5 serving as an "iDRAC" for your laptop.
- **Remote KVM:** Low-latency 1080p capture (via CSI) for BIOS-level access over any network.
- **Dual 2.5GbE:** Isolated management and data networks using Realtek PCIe and USB 3.0 controllers.
- **Premium Build:** Internal Mean Well 200W PSU (IEC C14 inlet), CNC aluminum enclosure, and Noctua silent cooling.
- **Storage & Peripherals:** M.2 NVMe NAS and 1.3" OLED status display.

## Project Documentation

Detailed research and specifications are available in the [docs/](docs/) directory:

1. [Vision](docs/01_vision.md) - Competitive analysis and product goals.
2. [Feasibility](docs/02_feasibility.md) - Initial exploration and risk assessment.
3. [Architecture](docs/03_architecture.md) - Technical system design and signal paths.
4. [Specification](docs/04_specification.md) - Hardware BOM, power budget, and thermal strategy.
5. [Layout Strategy](docs/05_layout_strategy.md) - Modular core + daughterboard plan.
6. [Pinout Matrix](docs/06_pinout_matrix.md) - CM5 and FFC interface pinouts.
7. [Power States](docs/07_power_states.md) - Sequencing and regulator strategy.
8. [Hardware Validation](docs/08_hardware_validation.md) - Decoupling, Test Points, and Bring-up.

## Hardware Status

- [x] Phase 1: Conceptual Research & Vision
- [x] Phase 2: Technical Feasibility & Component selection
- [/] Phase 3: Hardware Design Preparation (Pinout, Sequencing)
- [ ] Phase 4: Schematic & PCB Design (KiCad)
- [ ] Phase 5: Enclosure CAD & Thermal Simulation
- [ ] Phase 6: Prototype Manufacturing

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
