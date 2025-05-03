# 🛠️ Intel ME / AMT Backdoor – Remote Control Below the OS

**Intel Management Engine (ME)** and **Active Management Technology (AMT)** are low-level subsystems embedded in most Intel chipsets, providing **out-of-band remote management** — even when the machine is powered off. When misconfigured, exploited, or backdoored, they allow **full remote control** below the operating system.

> ⚠️ ME is Ring -3 — it runs **before the BIOS**, has full access to memory, networking, and file systems, and is invisible to the OS. It’s used by APTs, insiders, and state-level implants.

---

## 🛠️ What It Does
- Intel ME is a microcontroller with its own OS (Minix-based) running on the Platform Controller Hub (PCH)
- AMT provides remote KVM, file access, power control, and more
- If enabled and reachable over LAN or Wi-Fi, attackers can:
  - Bypass disk encryption and OS authentication
  - Dump RAM, flash storage, and UEFI
  - Control keyboard, mouse, and screen remotely — even when OS is off

---

## 💣 Attack Paths

### 1. **Default/Weak AMT Credentials (CVE-2017-5689)**
- AMT admin web UI or Intel LMS running with default `admin/admin`
- Remote attacker logs in and enables full KVM/control remotely

### 2. **Insider Provisioning Abuse**
- Malicious IT staff or supply chain actor pre-configures AMT access
- Target has no indication it’s active — firmware shows “disabled” but provisioned via MEBx or LMS

### 3. **Backdoored or Compromised ME Firmware**
- Custom ROM image flashes backdoored ME firmware (e.g., via capsule hijack)
- Implants persist across formats and OS reinstalls

---

## 🧪 Detection Tips
| Indicator | Method |
|-----------|--------|
| ME active on system despite BIOS “disabled” setting | Inspect ME firmware status with `MEInfo`, CHIPSEC, or UEFITool |
| AMT listening on TCP 16992/16993 | Scan LAN with `nmap` or detect AMT beaconing traffic |
| KVM/IDE-R sessions occurring with no OS logs | Monitor physical input events and cross-reference power logs

---

## 🔐 Mitigation Strategies
- **Fully disable AMT/ME** in BIOS and firmware — if OEM allows
- Unprovision ME via MEBx setup menu or `unconfigure` command
- Monitor network for AMT ports (16992–16995, 623/664)
- Flash known-clean firmware images with ME region scrubbed or disabled (e.g., ME_Cleaner)
- Physically block or isolate management NIC from production network

---

> 🧠 Intel ME is the system **inside your system**. It runs when your OS doesn’t. It talks when your logs are off. And if it’s compromised — **you’ll never know.**

This is full-stack remote access from **deep inside the silicon.**
