# ⚡ Thunderbolt DMA Attacks – Breaking Memory from the Port

**Thunderbolt DMA attacks** exploit how Thunderbolt and PCIe interfaces allow **direct memory access (DMA)** to system RAM. By plugging in a malicious device, attackers can read or write system memory — bypassing the CPU, OS, and all software-based security — even if the device is “locked.”

> ⚠️ This is how attackers hijack laptops in seconds with a cable. It’s why **evil maid attacks** matter — even with full-disk encryption and no USB access.

---

## 🛠️ What It Does
- Thunderbolt (PCIe over USB-C) enables DMA-capable devices to directly access host memory
- Without IOMMU protection, or with vulnerable BIOS configurations, attackers can:
  - Read plaintext secrets from RAM (e.g. BitLocker keys, passwords)
  - Inject shellcode directly into kernel space
  - Hijack running OS sessions or inject persistent implants

---

## 💣 Attack Flow

### 1. Prepare Malicious Thunderbolt Device
- Use FPGA, modified Thunderbolt-to-Ethernet adapter, or tools like `PCILeech`
- Program device to scan, dump, or overwrite RAM regions

### 2. Plug into Target Machine
- If IOMMU/Kernel DMA Protection is off (common on older systems):
  - Device gains direct access to mapped memory ranges

### 3. Exfiltrate or Inject
- Dump login credentials, crypto keys, active sessions
- Inject kernel backdoor or patch security checks

✅ Bypasses password, disk encryption, even locked screen

---

## 🧪 Notable Tools
| Tool | Use |
|------|-----|
| [PCILeech](https://github.com/ufrisk/pcileech) | DMA memory read/write over PCIe/Thunderbolt |
| [StormKitty](https://github.com/krishpranav/stormkitty) | Leverages PCILeech for credential dumping |
| FPGA dev boards (e.g., Lattice, Xilinx) | Build custom DMA injectors

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Sudden Thunderbolt hotplug event with no user interaction | Monitor USB-C / PCIe port enumeration logs |
| Unauthorized memory access errors (with IOMMU on) | Check dmesg, Windows Event Log for DMA faulting |
| USB-C devices with unknown or invalid descriptors | Flag via USBGuard or Thunderbolt Security Level settings

---

## 🔐 Mitigation Strategies
- **Enable IOMMU** (Intel VT-d / AMD-Vi) in BIOS and OS
- Use **Kernel DMA Protection** (Windows 10+ with supported chipsets)
- Set Thunderbolt Security to **“User Authorization” or “No Access”**
- Disable Thunderbolt ports entirely in BIOS for air-gapped or secure systems
- Use port blockers, epoxy, or physically disable USB-C in secure zones

---

> 🧠 DMA attacks are about **speed and silence**. You don't need a login. You need **direct memory.**

This is how plugging in becomes **total compromise in under 30 seconds.**
