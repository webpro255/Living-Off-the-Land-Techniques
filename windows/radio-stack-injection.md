# 📡 Radio Stack Injection – Wireless Firmware Exploits

**Radio stack injection** targets the firmware of wireless interfaces — **Wi-Fi, Bluetooth, NFC, and cellular** — to exploit over-the-air vulnerabilities. These attacks inject code into the device through specially crafted packets, often with **no user interaction and no OS-level trace.**

> ⚠️ Real-world attacks like **BroadPwn** (Wi-Fi chip exploit), **BleedingTooth** (Bluetooth stack vuln), and **SweynTooth** (BLE injection) show that wireless chips are soft targets — and great entry points for persistent, stealth implants.

---

## 🛠️ What It Does
- Radio interfaces are controlled by separate firmware and often run independent OSes (e.g., ThreadX, FreeRTOS)
- Attackers exploit parsing bugs in these firmwares to:
  - Execute code on the chip itself
  - Escalate into main memory via shared DMA
  - Hijack the host system silently

---

## 💣 Attack Examples

### 1. **BroadPwn (CVE-2017-9417)**
- Vulnerability in Broadcom Wi-Fi chips
- Remote code execution via malformed Wi-Fi frames (no auth needed)
- Exploit chain:
  - Send rogue beacon or probe request
  - Trigger heap overflow in Wi-Fi firmware
  - Inject shellcode that escalates into host OS via shared memory

### 2. **BleedingTooth (CVE-2020-12351)**
- Linux kernel Bluetooth stack vulnerability
- Privilege escalation and remote code execution via crafted L2CAP packets
- Triggered via Bluetooth without pairing

### 3. **SweynTooth (Multiple CVEs)**
- Series of vulnerabilities in BLE SoC firmware (e.g., NXP, STMicro)
- Over-the-air denial of service or code execution
- Target: medical devices, smart locks, IoT gateways

---

## 🧪 Tools & Frameworks
| Tool | Use |
|------|-----|
| [InternalBlue](https://github.com/seemoo-lab/internalblue) | Reverse engineer Bluetooth baseband firmware |
| [LORCON](https://github.com/radiotap/lorcon) | Raw 802.11 frame injection |
| [GATTacker](https://github.com/securing/gattacker) | BLE man-in-the-middle/injection |
| [Frida / QEMU] | Dynamic firmware analysis

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Anomalous wireless frame types or rates | Monitor with Wireshark, Kismet, or custom RF monitors |
| Crashes or resets in radio firmware | Check kernel logs, driver-level interrupts |
| Unexpected memory access from wireless SoC | Monitor DMA boundaries and shared buffer activity

---

## 🔐 Mitigation Strategies
- Update wireless chip firmware and drivers regularly
- Physically disable unused radio interfaces in high-security zones
- Isolate memory access from radio SoCs (firmware or IOMMU configs)
- Use known-good firmware baselines and secure boot (if supported by chip)

---

> 🧠 They sent a packet. You lost a system. Radio firmware is the **forgotten OS** inside every device — and it's wide open to those who whisper in 802.11 and Bluetooth.

Welcome to silent warfare — where exploits fly, and devices die.
