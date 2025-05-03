# 🔌 Synthetic Firmware Implants via Device Emulation (BadUSB & DMA Attacks)

**Synthetic firmware implants** exploit hardware interfaces like **USB, PCIe, or Thunderbolt** to deliver firmware-level payloads through **emulated or malicious devices**. This technique bypasses OS defenses by pretending to be a trusted piece of hardware — and then injecting or overwriting firmware from the inside.

> ⚠️ Used in attacks like BadUSB, PCILeech DMA implants, and supply-chain device tampering. It turns an innocent-looking peripheral into a **firmware-injecting parasite**.

---

## 🛠️ What It Does
- Attacker crafts a **malicious hardware device** (or flash a real one)
- The device **emulates a trusted interface** (keyboard, NIC, disk, etc.)
- On connection, the device:
  - Performs **DMA attacks** (PCIe/Thunderbolt)
  - Injects firmware into SPI, TPM, or UEFI
  - Alters boot process or delivers persistent payloads

---

## 💣 Attack Vectors

### 1. **BadUSB-Style USB Implants**
- Modify firmware of USB microcontroller (e.g., Phison, ATmega32u4)
- Emulate keyboard or disk, inject payloads automatically
✅ Can drop stage-0 malware without user interaction

### 2. **PCILeech DMA Injection (PCIe/Thunderbolt)**
- Plug into Thunderbolt or PCIe slot
- Use DMA access to write directly into RAM or firmware memory
✅ Can inject SMM/UEFI backdoors or disable Secure Boot

### 3. **Supply Chain Device Mods**
- Modify firmware of legitimate USB/network device
- On first use, implant persistent rootkit into BIOS or EFI partition
✅ Used in high-profile espionage campaigns

---

## 🧪 Tools & Examples
| Tool | Purpose |
|------|---------|
| [USB-Rubber-Ducky](https://shop.hak5.org) | Keystroke injection over USB |
| [PCILeech](https://github.com/ufrisk/pcileech) | DMA memory injection |
| [HackRF/USB Armory] | Radio-based or USB microcontroller implants |
| [Thunderclap PoCs](https://thunderclap.io) | Thunderbolt DMA attacks

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unusual device enumeration or rapid plug/unplug | Monitor device manager logs or USB debug events |
| High-speed memory access from non-CPU sources | Use IOMMU audit tools or hardware DMA monitors |
| Unexpected changes in UEFI/firmware | Validate firmware hashes, check for modified bootloaders

---

## 🔐 Mitigation Strategies
- Enable IOMMU (Intel VT-d / AMD-Vi) and Thunderbolt security levels
- Restrict USB device usage via device control policies
- Disable unused PCIe lanes, Thunderbolt ports, or legacy USB ports in firmware
- Audit connected devices for rogue firmware (where possible)
- Use tamper-proof hardware and trusted supply chain components

---

> 🧠 This is the **hardware trojan horse** — a trusted device becomes your worst enemy. Synthetic firmware implants can ride in on a flash drive, and leave a backdoor in your BIOS.

Plug it in. Lose everything. Unless you knew this attack was even possible.
