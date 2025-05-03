# 🧬 Malicious Device Firmware – Weaponizing USB & Peripheral Hardware

**Malicious device firmware** turns everyday peripherals — USB drives, keyboards, NICs, and more — into stealthy attack vectors by modifying their embedded firmware. Once plugged in, these devices deliver payloads, proxy data, or impersonate trusted hardware — all without installing software on the host.

> ⚠️ Firmware implants live in **controller chips**, not the OS. They persist across formats, rebuilds, and reboots — often invisible to AV and forensic tools.

---

## 🛠️ What It Does
- Flashes malicious firmware to microcontrollers in common peripherals
- Uses HID emulation, mass storage spoofing, or USB descriptor tampering to:
  - Deliver staged malware without user interaction
  - Execute keystroke injection (BadUSB)
  - Act as covert C2 channel or network proxy
  - Evade traditional device control policies

---

## 💣 Attack Examples

### 1. **BadUSB (HID Emulation)**
- Flashes USB firmware to pretend it’s a keyboard
- Injects keystrokes to open PowerShell, download payloads, etc.

### 2. **Hidden Partition Loaders**
- USB shows a normal partition
- Firmware switches to hidden partition after specific trigger
- Used to drop payloads or C2 beacon silently

### 3. **Network Adapter Proxying**
- Flash malicious firmware onto USB Ethernet/NIC dongles
- Traffic interception, DNS manipulation, or exfil gateway

### 4. **Supply Chain Preimplantation**
- Devices arrive infected from the manufacturer or reseller
- Used in nation-state hardware interdiction campaigns

---

## 🧪 Notable Tools / Frameworks
| Tool | Use |
|------|-----|
| [RUsh](https://github.com/samyk/usbdriveby) | Multi-function BadUSB tool |
| [HackRF + USBProxy](https://github.com/dominicgs/USBProxy) | Emulate USB devices for testing |
| [Facedancer](https://github.com/usb-tools/facedancer) | Custom USB stack for fuzzing/emulation |
| [Phison PS2251 Firmware Tool](https://github.com/adamcaudill/Psychson) | Backdoor common USB drives

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected HID device enumeration | Log USB descriptors on plug-in (VID/PID, class) |
| Firmware not matching known hashes | Use USBGuard, CHIPSEC, or hardware-based inventory scanners |
| Traffic via USB-based NICs | Alert on new interfaces or rogue DHCP clients

---

## 🔐 Mitigation Strategies
- Restrict unknown USB peripherals via device control or group policy
- Physically disable unused ports or use USB condoms/data blockers
- Maintain known-good firmware inventory (hashes + VID/PID pairs)
- Validate device firmware during procurement (especially for secure zones)

---

> 🧠 Firmware implants don’t live in memory — they live **in the plastic**. You didn’t download malware — **you plugged it in.**

This is how attackers turn your trusted devices into **hardware-based traitors.**
