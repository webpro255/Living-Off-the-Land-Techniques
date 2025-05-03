# 🧠 Hardware Interrupt Hijacking (SMM Backdoors)

**System Management Mode (SMM) Hijacking** is an ultra-stealth attack technique where malicious code is injected into **SMM handlers** — giving the attacker execution in a protected CPU mode *below the OS, hypervisor, and firmware scanners*.

> ⚠️ This is black-level stealth. Used by advanced APT research to implant backdoors in CPU interrupt handlers. Virtually undetectable by traditional defenses. Runs in **ring -2** (below kernel and hypervisor).

---

## 🛠️ What It Does
- SMM is triggered via **SMI (System Management Interrupt)**
- Executes code in a hidden CPU mode used for thermal control, power management, or firmware updates
- Attacker hijacks SMM handlers or registers a custom SMI trigger
- Code executes invisibly — OS, AV, EDR, and hypervisors cannot see or stop it

---

## 💣 Attack Flow

### 1. Gain Ring-0 or Firmware-Level Access
- Flash an SMM-capable payload via:
  - SPI flash writer
  - Signed driver exploit (e.g., RTCore64.sys, capcom.sys)
  - UEFI runtime exploit

### 2. Locate SMM Handler Table
- Parse System Management RAM (SMRAM) or locate `SMI_CMD` via ACPI
- Hook existing handler or inject new one to hijack execution

### 3. Trigger Custom SMI
- Send interrupt (e.g., via port `0xB2` or `0xB3`)
```c
__outbyte(0xB2, 0xAA); // Trigger malicious SMI handler
```
✅ Payload executes in SMM — invisible to OS or user-mode debuggers

---

## Capabilities of an SMM Rootkit
- Log keystrokes at hardware level
- Patch kernel or hypervisor memory silently
- Bypass Secure Boot / BitLocker
- Disable hardware protections like BIOS lock or UEFI write protections

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Abnormal SMI triggers (e.g., port 0xB2 activity) | Monitor I/O port access via hypervisor or PCI bridge sniffing |
| Modified SMM region or unlocked SMRAM | Use `Chipsec` to validate SMRAM protections |
| Unexpected CPU mode transitions | Detect via VMExit or trace logs in forensic hypervisor

---

## 🔐 Mitigation Strategies
- Lock SMRAM with BIOS settings (`SMRR` + `D_LCK` flags)
- Use Intel BIOS Guard, AMD SHS, or HP Sure Start (firmware verification)
- Disable or restrict SMI interfaces in production devices
- Enable SPI flash write protection (hardware or firmware level)
- Deploy firmware attestation and watchdog-based SMI audits

---

> 🧠 SMM backdoors are **the invisible overlords of the system**. You don’t see them. You can’t debug them. And when implanted, **no amount of OS forensics will save you**. They are the ghost in the chip.

Welcome to ring -2 warfare.
