# 🏛️ GPO Abuse - Group Policy as Persistence

**Group Policy Object (GPO) Abuse** is a powerful enterprise-level persistence and control technique. Once an attacker gains **domain admin or GPO edit rights**, they can push **malware, scripts, or backdoors** to every system in the domain — silently and persistently.

> ⚠️ This is how nation-state actors and advanced red teams take over entire fleets. Not detectable by EDR on endpoints alone — needs centralized GPO auditing.

---

## 🛠️ What It Does
- Modifies domain-linked Group Policy Objects (GPOs)
- Injects malicious settings (e.g., logon scripts, scheduled tasks, service changes)
- Impacts all machines/users in the target OU
- No alerts on endpoints unless additional logging is enabled

---

## 💣 Persistence Tactics Using GPO

### 1. Push a Logon Backdoor Script
```cmd
\DOMAINCONTROLLER\SYSVOL\domain.local\Policies\{GPO-GUID}\User\Scripts\Logon\evil.vbs
```
And in GPMC:
```text
User Configuration > Windows Settings > Scripts (Logon/Logoff) > Logon: evil.vbs
```
✅ Runs payload every time domain user logs in — auto-distributed to all affected OUs

---

### 2. Deploy Scheduled Task via GPO
```text
Computer Configuration > Preferences > Control Panel Settings > Scheduled Tasks
```
✅ Launches malware on boot or user login across entire domain

---

### 3. Modify Registry Settings Remotely
```text
User Configuration > Preferences > Windows Settings > Registry
```
- Set persistence via Run key
- Set debugger hijack
✅ Red team implants via GPO are hard to trace per endpoint

---

### 4. Distribute Malicious EXE or DLL
```cmd
Copy payload.exe to \domain\SYSVOL
Use GPO to launch it via script, Run key, or scheduled task
```
✅ Signed EXE? Even stealthier

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected GPO changes | Monitor GPO audit logs on domain controllers |
| Scripts in SYSVOL not tied to official policies | Regularly diff SYSVOL and GPO entries |
| GPO adding scheduled tasks or Run keys | Alert when new task policies are created from unusual admin accounts

---

## 🔐 Mitigation Strategies
- Enforce tiered admin model (no daily users with GPO rights)
- Monitor Group Policy changes via Event ID 5136 (Directory Services change)
- Use tools like BloodHound to audit who can edit GPOs
- Regularly audit logon scripts and scheduled tasks pushed via GPO

---

> 🧠 If you own GPO, you own the enterprise. This is one of the **loudest weapons in the quietest place** — an invisible hand pushing malware domain-wide, trusted by every machine.
