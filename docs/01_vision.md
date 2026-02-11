# Product Vision: Cyber-Dock

> **The "God Mode" Dock.**
> A single device that grants total physical and digital control over your workstation infrastructure.

## The Problem

Modern docks are passive. They expand ports but offer no intelligence. If your laptop crashes, the dock is useless. If you need to re-image your machine remotely, you can't. If you want to run a local server, you need a separate device.

## The Solution

**Cyber-Dock** fuses three devices into one premium chassis:

1. **High-End USB-C Dock:** 100W charging, 4K video, fast peripheral access.
2. **IP-KVM (PiKVM):** BIOS-level remote control, screen capture, and keyboard emulation.
3. **Edge Server (NAS):** An always-on Linux server for Docker, VPN, and storage.

## Target Audience / Persona

- Works from home or travels frequently.
- Manages complex local environments (Kubernetes, heavy compilations).
- Needs enterprise-grade reliability (ECC memory, reliable power).
- Hates clutter. Wants one cable to rule them all.

## User Journey: "The Portable Office"

1. **09:00 AM:** Tim is in Tokyo. His workstation laptop in New York freezes during a Windows update.
2. **09:05 AM:** Tim logs into his **Cyber-Dock** via Tailscale on his iPad.
3. **09:06 AM:** He opens the KVM dashboard. He sees the Windows BSOD.
4. **09:07 AM:** He toggles the "Force Power Off" GPIO relay on the dock. The workstation cuts power. It turns back on.
5. **09:08 AM:** He enters the BIOS menu remotely using the KVM keyboard.
6. **09:10 AM:** He mounts a "Rescue Disk" ISO stored on the Dock's NVMe drive as a virtual USB stick.
7. **09:15 AM:** The workstation boots from the extensive Dock library. Tim repairs the bootloader.
8. **09:30 AM:** Workstation is back online. Tim sips coffee in Tokyo.

## Design Philosophy

- **Industrial, Not Gamer:** Matte black anodized aluminum. Minimalist LEDs. No RGB rainbow.
- **Over-Engineered:** 200W internal PSU. 6-layer PCB. 2.5GbE everywhere.
- **Open Platform:** Runs standard Linux (Arch/Debian). You own the root password.

## Feature Matrix

- **Must-Have (MVP):**
  - [x] 100W PD Charging (TI TPS65987D).
  - [x] PiKVM Integration (CM5 + CSI Capture).
  - [x] Dual 2.5GbE Networking (Isolated).
  - [x] NVMe Storage (PCIe Gen 2 via Switch).
- **Stretch Goals (v1.5):**
  - [ ] OLED Status Display (IP, Power Output, Temps).
  - [ ] Hardware Hardware Watchdog.
- **Future (v2.0):**
  - [ ] Front-panel SD Card Reader.
  - [ ] USB4 / Thunderbolt 4 (40 Gbps).
  - [ ] POE++ Input (Power the whole dock from a switch).
  - [ ] Dual HDMI Inputs.
