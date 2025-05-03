# 🏴‍☠️ DCShadow - Stealth AD Replication Attack

**DCShadow** is an advanced Active Directory attack that allows an attacker to simulate a **domain controller** and push **malicious directory changes** (like user rights or SID history) directly into AD **without generating typical logs**.

> ⚠️ This is top-tier stealth. Used by APTs to escalate, persist, or backdoor AD without touching a real DC object or triggering security alerts.

---

## 🛠️ What It Does
- Temporarily registers a rogue Domain Controller (DC)
- Pushes fake replication data using Microsoft protocols
- Writes directly into AD **without using standard tools or APIs**
- Evades detection tools that monitor AD with `Security Event Logs`

---

## 💣 Real-World Exploitation

### 1. Requirements
- Domain Admin or SYSTEM on at least one domain-joined machine
- Access to tools like `Mimikatz`, `Impacket`, or `DCShadow.ps1`

---

### 2. Use Mimikatz to Launch DCShadow
```cmd
privilege::debug
lsadump::dcshadow /object:CN=admin,CN=Users,DC=corp,DC=local /attribute:sidHistory /value:S-1-5-21-... /push
```
✅ Adds SIDHistory to user silently — instant privilege escalation

---

### 3. Abuse with Other Attributes
- Add user to privileged groups via `memberOf`
- Modify `logonHours`, `description`, `userAccountControl`
- Push rogue SPNs, SIDHistory, ACLs
✅ No LDAP logs, no PowerShell traces — it’s seen as internal replication

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unregistered DCs sending replication traffic | Monitor replication metadata and replication source IPs |
| New domain replication partners that don't exist in AD | Validate all replication metadata entries with `repadmin /showrepl` |
| Use of `lsass` access tools on non-DC machines | Alert on usage of DCShadow-compatible binaries like `mimikatz`, `kekeo`

---

## 🔐 Mitigation Strategies
- Monitor DC registration events and replication partner changes
- Lock down Domain Admin access — rotate and isolate
- Use tiered admin model to separate domain-level and workstation-level creds
- Disable unnecessary RPC/DCOM communication across workstations

---

> 🧠 DCShadow is **domain-wide ghostwriting**. It’s not a vulnerability — it’s abusing the way AD trusts its replication. If attackers pull this off, they can write anything into AD — and leave no trail. This is the shadow that redefines the directory.
