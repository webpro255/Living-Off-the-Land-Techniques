# 🧬 SMM Rootkit – Implants in System Management Mode (Ring -2)

**System Management Mode (SMM)** is a special CPU mode designed for firmware-level system control and hardware abstraction. SMM runs in **Ring -2**, beneath the operating system and hypervisor, and is entirely opaque to both. When compromised, it becomes the **perfect rootkit** — invisible, persistent, and all-powerful.

> ⚠️ SMM rootkits have been demonstrated in the wild and used in nation-state operations. They survive OS reinstalls, bypass Secure Boot, and **operate without leaving a trace in memory or logs.**

---

## 🛠️ What It Does
- SMM uses a protected memory region called **SMRAM** to store code and data
- Triggered via **System Management Interrupts (SMIs)**
- A malicious implant in SMRAM can:
  - Intercept interrupts, keystrokes, and firmware calls
  - Patch OS or hypervisor memory in real time
  - Bypass all AV/EDR and OS logging

---

## 💣 Attack Paths

### 1. **SMRAM Injection via BIOS Flashing**
- Modify UEFI firmware to include SMM payload (DXE driver or runtime hook)
- Flashed via capsule update, SPI flash tool, or insider provisioning

### 2. **SMM Vulnerabilities (CVE Chains)**
- Exploit bugs in SMI handlers (e.g., unchecked memory access)
- Escalate from OS or hypervisor into SMRAM via vulnerability (e.g., CVE-2020-8705)

### 3. **Boot Time SMM Hijack**
- Intercept or replace SMI vector table during early boot
- Redirect interrupts to backdoored code stored in SMRAM

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected SMI frequency or behavior | Use Intel System Debugger or SMM profiling tools |
| SMRAM tampering | Use `chipsec` to validate SMRAM region integrity |
| Hooked interrupt handlers from outside OS | Memory forensics or hardware-assisted introspection (HVI)

---

## 🔐 Mitigation Strategies
- Lock SMRAM with `SMRR` (System Management Range Registers)
- Enforce BIOS write protection and SPI flash lockdown
- Use BootGuard + measured boot to validate firmware integrity
- Monitor and validate SMI behavior in high-assurance environments

---

> 🧠 SMM is the CPU’s master mode — a place no OS, hypervisor, or endpoint tool can see. Once it’s infected, **everything above it is a puppet.**

This is **firmware-level puppeteering** from the shadows of Ring -2.
