# 🔗 Signed Driver Chainloading - Dual-Stage Kernel Evasion

**Signed Driver Chainloading** is a cutting-edge kernel attack method where an attacker uses one **vulnerable signed driver** to **load and execute a second, stealth driver** — all while bypassing driver registration, AV, and Secure Boot policies.

> ⚠️ Used by advanced ransomware crews and APTs (e.g., BlackCat, Scattered Spider). It gives direct access to kernel memory and stealth driver implants **without leaving a footprint on disk or the registry**.

---

## 🛠️ What It Does
- Stage 1: Load a **known vulnerable but signed driver** (e.g., `RTCore64.sys`)
- Use it to execute memory-mapped code or load a second (unsigned/stealth) driver directly into memory
- Stage 2: The stealth driver performs kernel tampering, AV bypass, or implants, but **never touches disk or gets registered with Windows**

---

## 💣 Real-World Attack Flow

### 1. Load Stage-1 Driver (e.g., RTCore64.sys)
```cmd
sc create vulnsvc binPath= "C:\drivers\RTCore64.sys" type= kernel
sc start vulnsvc
```
✅ This vulnerable MSI Afterburner driver is signed and trusted by Windows

---

### 2. Use IOCTL to Map Stage-2 Shellcode
- Tools like `kdmapper`, `drvmap`, or custom C code
- Write malicious driver payload directly into kernel memory using IOCTL calls
```c
DeviceIoControl(hDriver, IOCTL_CODE, buffer, bufsize, out, outsize, &ret, NULL);
```
✅ Stage-2 implant driver runs live in memory without registration or file drop

---

### 3. Stage-2 Driver Capabilities
- Kill EDR callbacks (e.g., `ObRegisterCallbacks`, `PsSetLoadImageNotifyRoutine`)
- Hook SSDT or syscalls
- Hide processes, files, registry keys
- Dump LSASS or patch token privileges

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Known vulnerable driver loads (Event ID 6) | Use Sysmon or ETW to flag drivers like `RTCore64.sys`, `gdrv.sys` |
| IOCTL abuse from userland tools | Monitor for usage of `DeviceIoControl` with known exploit codes |
| EDR hooks disabled without explanation | Alert on sudden loss of `Ob*`, `Ps*`, or `Etw*` callbacks

---

## 🔐 Mitigation Strategies
- Blocklist known vulnerable drivers (Microsoft block policy or custom Defender rule)
- Enable **Memory Integrity (HVCI)** to prevent unsigned driver memory execution
- Alert on unsigned kernel memory regions
- Audit tools like `kdu.exe`, `kdmapper.exe`, `drvmap.exe`

---

> 🧠 Chainloading is **weaponized trust**. The attacker brings their own loader, uses your OS to bless it, and then drops a ghost in the kernel. No file. No log. No mercy.

This is how kernel-space malware stays invisible — and how real red teams break every security model below the surface.
