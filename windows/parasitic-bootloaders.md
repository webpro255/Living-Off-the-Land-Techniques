# 🧟 Parasitic Bootloaders – Hijacking the Boot Chain Without Replacing It

**Parasitic bootloaders** don’t replace the system’s official bootloader — they silently attach themselves to it. These implants operate like firmware parasites: intercepting early boot processes, modifying memory on-the-fly, and passing control to the legitimate bootloader so everything looks normal.

> ⚠️ This isn’t a bootkit that takes over — it’s a ghost in the chain. **It rides alongside** the trusted loader, patching memory, flipping flags, and remaining nearly invisible.

---

## 🛠️ What It Does
- Hooks into bootloader stages (UEFI DXE, PEI, GRUB, or Windows Boot Manager)
- Patches kernel, Secure Boot variables, or trust measurement code *just in time*
- Leaves official bootloader and firmware **untouched** — so hashes still match

---

## 💣 Attack Methods

### 1. **GRUB Hook Injection**
- Modifies GRUB modules (e.g., `normal.mod`) to execute implant before handing off to OS kernel
- Restores original control flow after patching kernel or ACPI tables

### 2. **UEFI DXE Driver Parasite**
- Load custom DXE driver alongside other UEFI modules
- Hooks boot services like `ExitBootServices()` or `LoadImage()`
- Implant ends execution before OS takes over, leaving no trace in RAM

### 3. **Windows Bootloader Hijack (Winload.efi)**
- Inject shellcode into `winload.efi` or `bootmgr` that modifies kernel boot params (e.g., disables PatchGuard, enables unsigned drivers)
- Uses runtime relocation to preserve boot integrity logs

---

## 🕵️‍♂️ Detection Tactics
| Signal | Method |
|--------|--------|
| Early memory tampering before OS logs start | Use hardware-assisted introspection (e.g., Intel TXT + TPM logs) |
| Slight delay or additional steps in bootloader behavior | Boot timing profiling across baselines |
| Non-standard memory access patterns during handoff | Trace `ExitBootServices()` stack with EFI monitoring tools

---

## 🔐 Mitigation Strategies
- Enable **measured boot** with TPM + DRTM (verify hash chain end-to-end)
- Validate GRUB/UEFI modules and measure DXE driver load order
- Use `chipsec` to monitor firmware regions and boot path
- Physically write-protect bootloader storage (SPI lockdown)

---

> 🧠 Parasitic bootloaders are the perfect passengers: invisible, non-invasive, and lethal. They don’t replace trust — they ride it.

This is stealth that walks with your OS — and leaves before you even notice.
