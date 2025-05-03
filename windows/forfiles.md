# 📁 Forfiles - Living Off the Land

**forfiles.exe** is a native Windows command-line tool used to select and act on files based on criteria like file age. It’s mostly used by administrators for cleanup — but attackers abuse it to **trigger payloads based on timestamps**, or **hide execution under file maintenance scripts**.

> ⚠️ Only use this in lab environments with clear authorization.

---

## 🛠️ What It Does
- Selects files by modified/created date
- Executes a command for each matching file
- Useful for file-based logic (dropper chains, timed persistence)
- Can blend in with scheduled cleanup scripts

---

## 💣 Common LotL forfiles Techniques

### 1. Trigger Execution Based on File Age
```cmd
forfiles /p "C:\Logs" /m *.log /d -7 /c "cmd /c calc.exe"
```
✅ Executes calc.exe if it finds any `.log` files older than 7 days

---

### 2. Use for Lateral Movement or Recon
```cmd
forfiles /p C:\Users /s /m *.ps1 /d -1 /c "cmd /c type @path >> C:\loot.txt"
```
✅ Harvests all PowerShell scripts modified in the last day

---

### 3. Create Timed Logic to Hide Payload
```cmd
forfiles /p C:\Payloads /m backdoor.ps1 /d -2 /c "powershell.exe -ExecutionPolicy Bypass -File @path"
```
✅ Launches a backdoor script only if it’s at least 2 days old

---

### 4. Delete Files Quietly by Age
```cmd
forfiles /p "C:\Temp" /s /m *.tmp /d -5 /c "cmd /c del /q @path"
```
✅ Removes files quietly — often used to clean traces

---

### 5. Hide Execution in Scheduled Tasks
```cmd
schtasks /create /tn "Windows Cleanup" /tr "forfiles /p C:\Public /m update.exe /d -3 /c \"cmd /c @path\"" /sc daily /f
```
✅ Looks like a maintenance task but executes malware

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| forfiles executing binaries | Rare in normal IT tasks — monitor `@path` resolving to `.exe`, `.ps1`, or `.hta` |
| Obfuscated scheduled task logic | Alert on scheduled tasks using `forfiles` in `/tr` command |
| Deletion with `/d` flag | Could be used for log tampering or evidence removal

---

## 🔐 Mitigation Strategies
- Restrict `forfiles.exe` if not used in your environment
- Log scheduled task creation and flag `forfiles` usage
- Hunt for use of `@path` triggering anything outside `.txt`, `.log`, or `.csv`
- Apply behavior-based alerts around rare CLI tools in user directories

---

> 🧠 Forfiles is rarely used by attackers, which makes it more stealthy when it is. Pair with `schtasks`, `reg.exe`, or `powershell` for sleeper attacks and timestamp-based logic.
