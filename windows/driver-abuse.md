# 🪓 Trusted Binary Proxy Execution (Signed Driver Abuse)

**Trusted Binary Proxy Execution** is an evasion and privilege escalation technique that abuses **Microsoft-signed or vendor-signed drivers** to execute malicious code, disable protections, or escalate to kernel-mode — **without detection**.

> ⚠️ Used in the wild by ransomware gangs (e.g., BlackByte, LockBit), APTs, and red teams to kill EDR, dump LSASS, or gain SYSTEM/root via kernel access.

---

## 🛠️ What It Does
- Loads a known **vulnerable signed driver** (often abused from gaming or OEM software)
- Driver is trusted by Windows — bypasses Secure Boot and AV/EDR checks
- Attacker uses driver to:
  - Access arbitrary memory (kernel read/write)
  - Unhook EDR telemetry
  - Dump credentials from LSASS

---

## 💣 Examples of Abused Drivers
| Driver File | Known Abuse | Source |
|-------------|-------------|--------|
| `capcom.sys` | Kernel R/W | Capcom game anti-cheat |
| `RTCore64.sys` | EDR kill + mem patch | MSI Afterburner |
| `gdrv.sys` | Arbitrary write | Gigabyte software |
| `dbutil_2_3.sys` | Elevation exploit | Dell driver pack |

---

## 🔧 Exploitation Workflow

### 1. Load Vulnerable Driver
Requires admin rights or trick (e.g., sideloading + service registration):
```cmd
sc create vulnsvc binPath= "C:\drivers\capcom.sys" type= kernel
sc start vulnsvc
```
✅ Loads signed driver — OS trusts it

---

### 2. Send IOCTLs to Abuse It
Use tooling like:
- [`kdmapper`](https://github.com/TheCruZ/kdmapper)
- [`drvmap`](https://github.com/ufrisk/DrvMapper)
- Custom exploit scripts (Python/C++)
✅ Gain direct access to kernel memory, kill EDR hooks, or inject shellcode

---

### 3. Clean Up / Maintain Access
- Remove service entry
- Use newly gained SYSTEM/kernel access to establish persistence

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unknown drivers loading into kernel | Use Sysmon Event ID 6 (Driver Loaded) + blocklist known vulnerable drivers |
| Non-Windows drivers loaded from unexpected locations | Alert on `C:\Users`, `Temp`, `Public` driver loads |
| EDR tampering + logging silence after driver load | Monitor for sudden logging drop or hook evasion

---

## 🔐 Mitigation Strategies
- Enable **Memory Integrity / HVCI** (blocks unsigned or bad signed drivers)
- Blocklist vulnerable drivers via Microsoft recommended driver block policy
- Use Device Guard and Code Integrity to enforce strict kernel driver policies
- Monitor driver load events with Sysmon and Windows Defender ATP

---

> 🧠 Trusted driver abuse isn’t a trick — it’s weaponized trust. If attackers can bring their own driver, they bring their own rules. And your EDR might not even see it happen.

This is **how ransomware turns off your eyes before it attacks.**
