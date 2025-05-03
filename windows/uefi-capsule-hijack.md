# 💉 UEFI Capsule Hijack – Compromising Firmware Updates in Transit

**UEFI Capsule Updates** are the modern method of delivering firmware upgrades through the OS — often via Windows Update (Windows Capsule), Linux `fwupd`, or vendor tools. Attackers can hijack or replace these update payloads **during delivery or staging**, injecting a UEFI-level implant that’s signed, trusted, and invisible.

> ⚠️ This is how backdoors are delivered **without dropping files on disk**, and why firmware is increasingly the **final frontier of persistence.** The system updates itself — into compromise.

---

## 🛠️ What It Does
- UEFI updates come in **capsule format** (e.g., `.cap`, `.bin`) and are signed by OEMs
- Delivered through OS vendor (Microsoft, Linux distros), device utilities, or IT push tools
- Attackers compromise the capsule **before** it is flashed, inserting a backdoored DXE driver or SMM payload

---

## 💣 Attack Paths

### 1. **Intercept Windows Update Firmware Capsules**
- Man-in-the-middle (e.g., enterprise proxy, supply chain) modifies `DeviceFirmwareUpdate` packages
- Re-signs with **stolen or leaked OEM cert** (or one accepted by BIOS chain)
- Flashed via standard Windows Update mechanism

### 2. **Hijack `fwupd` on Linux**
- Replace `.cab` or `.efi` payload on mirror or MITM Linux update traffic
- Custom `.esr` or `.cab` files are signed and queued for flashing during reboot

### 3. **Inside the OEM Toolchain**
- Malware or insider implants a modified capsule in the OEM support tool or driver package
- Can be staged **months before flashing**, waiting dormant

### 4. **Fake Capsule Injection (BYOC)**
- Social engineer or trojan a capsule-flashing utility with a rogue firmware payload
- User runs it thinking it's a legit BIOS update

---

## 🧪 Indicators of Compromise
| Indicator | Method |
|-----------|--------|
| Unexpected UEFI region changes post update | Compare pre/post SPI dumps with tools like `UEFItool`, `chipsec` |
| BIOS logs indicate update, but not sourced from official app | Cross-reference update path, OEM logs, and system event viewer |
| Presence of non-standard DXE or SMM modules | Enumerate modules in firmware with `UEFItool` or `efitools`

---

## 🔐 Mitigation Strategies
- Enforce capsule update verification with **measured boot + TPM attestation**
- Use secure boot **plus** BIOS write protection
- Validate capsules against OEM hashes before flashing
- Monitor firmware update paths (Windows Update logs, `fwupd` metadata)
- Avoid running BIOS updates from unverified local tools

---

> 🧠 A fake firmware update is the **most legitimate way to hack a machine**. No malware, no download — just a trusted OS update that implants the attacker before the OS even starts.

This is the dark side of auto-update: **your firmware asked for it.**
