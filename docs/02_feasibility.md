# Exploration: Cyber-Dock Concept

## Core Hypothesis

A USB-C docking station can serve as an autonomous **Edge Server** and **Out-of-Band Management (OOBM)** node, bridging the gap between a "dumb" port replicator and an enterprise BMC (Baseboard Management Controller).

## Why This Needs to Exist

1. **The "Laptop Server" Problem:** Developers run critical containers/VMs on laptops. When the laptop sleeps, reboots, or crashes, services die.
2. **The "Remote Worker" Problem:** Diagnosing a frozen laptop remotely is impossible without asking someone to "press the power button."
3. **The "Homelab" Problem:** PiKVMs are messy dongles hanging off the side. Docks are dumb bricks. Combining them saves space and adds utility.

## Detailed Use Cases

### 1. The "Indestructible" Dev Environment

- **Laptop:** Daily driver (VS Code, browser).
- **Dock (CM5):** Runs Docker host with Postgres, Redis, LocalStack.
- **Benefit:** Database persists when laptop reboots. Laptop connects via 2.5GbE network bridge.

### 2. Remote IT Support / "Helpdesk in a Box"

- **Scenario:** Remote employee's OS is corrupted.
- **Solution:** IT admin connects to Dock via Tailscale/WireGuard.
- **Action:** Mounts Windows ISO from Dock's NVMe as a USB drive. Reboots laptop into BIOS. Reinstalls OS remotely.

### 3. High-Security Air Gap

- **Scenario:** Sensitive workstation cannot touch the corporate LAN directly.
- **Solution:** Dock connects to corporate LAN (via CM5). Laptop connects ONLY to the Dock via USB-C.
- **Benefit:** CM5 *can* act as a hardware firewall/proxy (Rich Software Dependency: Requires transparent proxy & packet inspection stack).

## Feasibility Analysis

### Technical Feasibility: HIGH

- **Compute:** Raspberry Pi CM5 is well-supported.
- **Video Capture:** TC358743 (HDMI-to-CSI).
- **Power:** USB-C PD controllers (TPS65987D).
- **Networking:** PCIe-based Ethernet (RTL8125).

### Competitive Landscape

| Device | Type | Strengths | Weaknesses |
| :--- | :--- | :--- | :--- |
| **CalDigit TS4** | Thunderbolt Dock | 18 ports, 98W PD | "Dumb" device (No OS). |
| **PiKVM v3/v4** | KVM over IP | Great software | Not a dock (Messy dongle). |
| **Cyber-Dock** | **Hybrid** | **Autonomous OS + 100W PD** | Premium pricing. |

## Key Risks & Mitigations

| Risk | Impact | Mitigation |
| :--- | :--- | :--- |
| **Thermal Throttling** | CM5 + NVMe + Power ICs = Heat | CNC aluminum enclosure as heatsink + Noctua fan. |
| **USB Bandwidth** | Everything shares USB 3.0 uplink | Split critical traffic (mgmt LAN) to PCIe. |
| **Cost** | BOM > $400 | Position as premium/niche professional tool, not mass-market. |
| **Complexity** | 6-layer PCB, high-speed signals | Use proven reference designs (Geekworm, Waveshare foundations). |
