# 🕶️ Attrib - Living Off the Land

**attrib.exe** is a built-in Windows utility used to change file and folder attributes — like hidden, system, or read-only. While benign on the surface, attackers abuse it to **hide malware**, **delay detection**, and **obfuscate persistence payloads**.

> ⚠️ Use only in ethical red team labs. Misuse can cause system instability or concealment of malicious software.

---

## 🛠️ What It Does
- Sets file/folder attributes: hidden (`+h`), system (`+s`), read-only (`+r`)
- Removes those attributes (`-h`, `-s`, `-r`)
- Frequently abused in post-exploitation to cloak payloads

---

## 💣 Common LotL Attrib Techniques

### 1. Hide Executable Payload
```cmd
attrib +h +s C:\Users\Public\backdoor.exe
```
✅ Makes the file both hidden and system — invisible in most Explorer views

---

### 2. Obfuscate Persistence Scripts
```cmd
attrib +h C:\Users\Public\update.ps1
```
✅ Prevents casual discovery of scheduled or autorun scripts

---

### 3. Restore Visibility for Analysis
```cmd
attrib -h -s C:\Users\Public\backdoor.exe
```
✅ Defender technique to inspect previously hidden payloads

---

### 4. Target All Files in Directory
```cmd
attrib +h +s C:\Payloads\*.* /s
```
✅ Recursively hides all files — ideal for payload folders or droppers

---

### 5. Evade Scripts That Skip Hidden/System Files
```cmd
attrib +h +s +r C:\Users\Public\evil.bat
```
✅ Adds read-only status to delay analysis, recovery, or overwrite

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Executables with +h +s | Hunt for hidden/system `.exe`, `.ps1`, or `.dll` in user space |
| Recursively hidden folders | Flag rare full-directory attribute changes in `Public`, `%TEMP%`, or `%APPDATA%` |
| Reversion of attributes | Detect `attrib -h -s` followed by file execution

---

## 🔐 Mitigation Strategies
- Monitor file attribute changes with Sysmon or EDR tools
- Use scripts to scan for hidden executables in common drop zones
- Alert on write+hide operations combined (e.g., certutil → attrib)
- Restrict attribute changes via group policy or monitoring wrappers

---

> 🧠 Attrib is simple but deadly. It's often part of staged attacks, and often overlooked in forensic timelines. Use it with `certutil`, `schtasks`, or `msiexec` for extra stealth.
