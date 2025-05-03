# ⚡ DMA Injection & Persistence - Direct Memory Domination

**DMA Injection** is a stealth attack method that uses **Direct Memory Access (DMA)** over interfaces like **PCIe, Thunderbolt, FireWire**, or even **FPGA cards** to read/write memory **without CPU or OS involvement**. It’s used to plant implants, steal secrets, or modify OS behavior **without touching disk or triggering AV**.

> ⚠️ This is physical-access or peripheral-attack-tier hacking. It bypasses every ring, hooks nothing, and leaves zero forensic trace — unless you’re watching IOMMU events.

---

## 🛠️ What It Does
- DMA gives hardware direct access to system memory
- Attackers use a rogue PCIe/Thunderbolt/FPGA device to:
  - Read/write arbitrary RAM (implant shellcode or patch kernel)
  - Dump credentials (LSASS, BitLocker keys)
  - Hijack OS or inject stealth payloads live

---

## 💣 Attack Flow

### 1. Prepare Host with Unlocked DMA Access
- Most laptops and desktops allow DMA from plugged-in devices by default
- Disable IOMMU/VT-d? You're open to DMA
- Thunderbolt 3 in "legacy" or "permissive" mode? Also open

### 2. Plug In Malicious DMA Device
- Use:
  - PCILeech (via FPGA or LeechCore)
  - CactusCon DMA board (DIY style)
  - BadDMA (Raspberry Pi + PCI adapter)

### 3. Inject Live Payload or Dump Secrets
```bash
pcileech.exe dump -v -out memdump.raw
pcileech.exe inject -in my_payload.bin -v
```
✅ Injects shellcode or implants directly into kernel or user process memory
✅ Can persist until reboot, or write to disk silently

---

## 🧪 Tools & Hardware
| Tool | Interface |
|------|-----------|
| [PCILeech](https://github.com/ufrisk/pcileech) | PCIe, Thunderbolt, FPGA |
| [LimeSDR](https://limemicro.com) | Custom firmware injection over USB/PCIe |
| [BadDMA](https://github.com/aymankhalid/badDMA) | Raspberry Pi PCI DMA injector |
| [LeechCore](https://github.com/ufrisk/LeechCore) | Memory access library

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unusual DMA memory reads/writes | Monitor with IOMMU tracing or DMA guards |
| Devices bypassing driver stack | Watch for PCI/Thunderbolt enumeration without drivers |
| Unauthorized devices on bus | Audit device tree and unexpected PCIe bridges

---

## 🔐 Mitigation Strategies
- **Enable IOMMU/VT-d** in BIOS/UEFI for all machines
- Lock Thunderbolt ports (use "User Authorization" mode or disable legacy support)
- Use USBGuard or Thunderbolt security policies
- Disable PCIe hotplug if not needed
- Employ physical port locks and restricted BIOS options

---

> 🧠 DMA injection is the **closest thing to psychic hacking**. You don’t install anything. You don’t run anything. You just plug in and take control — because your system never learned how to say no.

Welcome to **unplugged warfare.**
