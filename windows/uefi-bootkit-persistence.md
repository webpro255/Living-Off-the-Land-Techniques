# 🧬 Bootkits + UEFI Rootkits - The Deepest Persistence

**Bootkits and UEFI rootkits** are the **most persistent and stealthy form of malware** — operating below the OS, modifying the bootloader or firmware itself. They allow attackers to survive **disk wipes, OS reinstalls, and even reformatting**.

> ⚠️ This is black badge level tradecraft. Used by APTs like **Sednit**, **Hacking Team**, and **Equation Group (NSA leak)**. Once implanted, they own the machine forever — and most AVs can’t even see them.

---

## 🛠️ What It Does
- Hijacks the system boot process before Windows loads
- Hooks the kernel or injects shellcode into memory during boot
- Resides in:
  - MBR (Master Boot Record) — legacy
  - VBR (Volume Boot Record)
  - EFI System Partition (ESP)
  - UEFI firmware (on SPI flash chip)

---

## 💣 Infection Techniques

### 1. MBR/VBR Bootkits
- Overwrite MBR with custom loader
- Hook OS kernel at boot to inject malware
✅ Legacy but still viable on older systems (non-UEFI)

### 2. EFI Partition Rootkits
- Drop malware to `EFI\Boot\bootx64.efi`
- Patch or replace `bootmgfw.efi`
- Persist via modified boot entries (via `bcdedit` or efibootmgr)

### 3. UEFI Firmware Rootkits (SPI Flash)
- Patch firmware directly (e.g., DXE driver hook)
- Write via vulnerable drivers (e.g., RWEverything, LoJack hijack)
✅ Even if disk is wiped, malware reloads every boot

---

## 🔧 Tooling & Examples
| Tool | Purpose |
|------|---------|
| [BootkitBareBones](https://github.com/Cr4sh/BootkitBareBones) | Minimal MBR/VBR bootkit for research |
| [Chipsec](https://github.com/chipsec/chipsec) | Detect UEFI implants, analyze firmware config |
| RWEverything | Read/write UEFI via Windows — often used in PoCs |
| EfiGuard | EFI bootloader hooking and runtime protection bypass

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected boot entries or changes to `bootx64.efi` | Monitor EFI partition for unauthorized changes |
| Modified firmware modules or checksum mismatches | Use `Chipsec` to scan SPI flash and UEFI layout |
| Early boot tampering / hooks detected in kernel space | Forensic inspection via memory dump before OS fully boots

---

## 🔐 Mitigation Strategies
- Enable Secure Boot + TPM attestation
- Lock SPI flash via firmware settings (BIOS lock)
- Disable legacy boot if not required
- Use hardware-backed integrity measurement tools (e.g., Intel Boot Guard, AMD SHS)
- Validate firmware via hashes (requires manufacturer support)

---

> 🧠 Bootkits aren’t malware — they’re **undetectable implants**. If AV is the guard dog, UEFI rootkits slip in through the walls. Most defenders **never check firmware** — and that’s exactly why the best attackers hide there.

This is where persistence becomes parasitic. Welcome to the real root.
