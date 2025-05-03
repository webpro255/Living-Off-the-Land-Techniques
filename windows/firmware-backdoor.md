# 🧬 Intel ME / AMD PSP Implants - Hardware-Level Backdoors

**Intel Management Engine (ME)** and **AMD Platform Security Processor (PSP)** are microcontrollers embedded into modern CPUs. Attackers who implant code here gain **full, stealthy control of the system — even when it’s powered off**.

> ⚠️ This is **beyond ring-0**. These subsystems run *below* the OS, below the BIOS, and outside your visibility. Used in APT research and firmware implants discovered in the wild (e.g., Vault7 leaks).

---

## 🛠️ What It Does
- Intel ME and AMD PSP operate independently from the OS
- Embedded in the PCH (Intel) or SoC (AMD)
- Have access to:
  - RAM
  - Disk
  - Network interfaces
  - Power management
- Can execute custom firmware that persists across:
  - BIOS resets
  - OS reinstalls
  - Disk wipes

---

## 💣 Real-World Attack Vectors

### 1. Intel ME Firmware Implant
- Use `me_cleaner` or RE tools to modify ME image
- Flash modified image back via SPI or tools like `Flashrom`
- Implant executes silently at every power-on

### 2. AMD PSP Exploitation
- Inject shellcode into PSP firmware region
- Leverage vendor backdoors or debug interfaces (e.g., JTAG, SPI flash)
- Tools are rare, but PoCs exist

### 3. Supply Chain/Physical Attacks
- Modify ME/PSP on a stolen or intercepted device
- Flash malicious firmware using internal programmer or vulnerability (e.g., CVE-2017-5705)

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| ME firmware checksum mismatch | Extract and hash with tools like `Intel FITC`, `ME Analyzer` |
| ME traffic on the network interface | Look for hidden interfaces or unexpected management packets |
| Power-on behavior that persists past clean OS/BIO wipe | Suspect root of trust compromise

---

## 🔐 Mitigation Strategies
- Enable Intel BootGuard (if supported)
- Use a ME-disabled platform (`me_cleaner` or coreboot)
- Flash vendor-verified firmware only
- Physically disable SPI reprogramming (BIOS lock switches, jumpers)
- Use open-source firmware stacks like `coreboot + heads` for auditability

---

> 🧠 ME/PSP implants are the **final boss** of persistence. They live below the OS, below the hypervisor, and outside any EDR or forensic tooling. If compromised, **the machine is no longer yours** — and you may never know.

This is the stuff nation-states *hope* you never ask about. Now you know.
