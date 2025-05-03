# 🕰️ Timestomping - Forensic Evasion via File Timestamp Manipulation

**Timestomping** is a technique where attackers alter the **Created**, **Modified**, and **Accessed** timestamps of files to **blend into the environment**, evade detection, and disrupt forensic timelines.

> ⚠️ Commonly used post-exploitation to make payloads look old or like system files. Used by APTs, ransomware actors, and stealthy malware droppers.

---

## 🛠️ What It Does
- Changes file timestamps to appear:
  - Older than the infection window
  - Matching legitimate system binaries
  - Like they’ve never been touched
- Breaks correlation between action and artifact

---

## 💣 Example: Match Payload to System File Timestamp

### 1. Reference a Legitimate File
```powershell
(Get-Item C:\Windows\System32\cmd.exe).CreationTime
```
✅ Use this as the timestamp blueprint

### 2. Apply It to Your Payload
```powershell
$src = Get-Item C:\Windows\System32\cmd.exe
$target = Get-Item C:\Users\Public\payload.exe
$target.CreationTime = $src.CreationTime
$target.LastWriteTime = $src.LastWriteTime
$target.LastAccessTime = $src.LastAccessTime
```
✅ `payload.exe` now appears to be from 2009 (or whenever your system EXE was created)

---

### 3. Manual Timestomp via `SetFileTime` (C++)
Use WinAPI for stealth C/C++ payloads:
```cpp
SetFileTime(hFile, &ftCreation, &ftAccess, &ftWrite);
```
✅ Often used by malware builders and loaders

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| File timestamps significantly older than OS install | Compare with install date or first boot log |
| Files with system dates in user folders | Alert on `2010`-era files in `%TEMP%`, `Downloads`, `Public` |
| Timestamps out of sequence | Created > Modified or Accessed < Created — bad hygiene is a giveaway

---

## 🔐 Mitigation Strategies
- Use EDRs that log file creation/modification events in real time
- Monitor key directories for newly added files regardless of timestamp
- Correlate first seen timestamps in SIEM with on-disk metadata
- Flag binaries where metadata does not match actual compile time (PE header check)

---

> 🧠 Timestomping doesn’t stop forensic analysis — but it slows responders down, breaks timelines, and helps malware **hide in plain sight**. It’s cheap, easy, and dangerous when defenders aren’t watching close enough.
