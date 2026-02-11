# Premium Component Specification (v2)

## Power Summary
- **Input:** IEC C14 power inlet (standard desktop cable).
- **Internal PSU:** Mean Well LOP-200-20 (200W, 20V DC output).
- **Charging:** 100W USB-C PD 3.0 (optimized for Dell XPS 9510).

## Networking
- **Management (CM5):** 2.5GbE (RTL8125 PCIe via ASM2806 switch).
- **Laptop Data:** 2.5GbE (RTL8156B USB 3.0).

## Peripherals
- **Storage:** M.2 NVMe (PCIe Gen 2 x1 shared).
- **SD Reader:** GL3224 USB 3.0 microSD (front panel).
- **Display:** 1.3" I2C OLED (SSD1306).
- **Audio:** PCM2704 USB DAC (3.5mm jack).
- **WiFi:** External RP-SMA antenna (MHF4 pigtail to CM5).

## BOM
Estimated **$405–565** per unit (quality-first focus).
