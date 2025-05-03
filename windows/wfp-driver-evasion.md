# 🔥 WFP Driver Abuse - Stealth Network Filtering

**Windows Filtering Platform (WFP)** is a powerful kernel-level network filtering system used by firewalls and security tools. Advanced attackers exploit it to **intercept, hide, or redirect traffic** — sometimes even using WFP to **disable or bypass AV/EDR communication**.

> ⚠️ WFP is built into the OS. If you control it — you control what packets the system sees. Used by malware like Turla, Flame, and stealth rootkits.

---

## 🛠️ What It Does
- Manipulates kernel network layers to:
  - Drop or hide C2 traffic
  - Filter EDR beaconing
  - Redirect or proxy network requests
- Works silently below user-mode tools like `netstat`, Wireshark, or EDR agents

---

## 💣 Real-World Use Cases

### 1. Drop AV/EDR Uploads
- Target AV traffic to vendor cloud (e.g., Defender to Microsoft)
- WFP blocks telemetry traffic while keeping internet alive
✅ Defender thinks it’s working — but never reports malicious activity

---

### 2. Hide C2 Beaconing
- Only allow outbound traffic to attacker C2 domain
- Drop all DNS/HTTP logs related to C2 from reaching EDR
✅ Exfiltration appears silent from endpoint logging tools

---

### 3. Covert Redirection (Proxying)
- Use WFP to silently forward traffic from legit apps (e.g., `Teams.exe`) to your implant
✅ Makes your payload look like Microsoft Teams

---

## 🔧 Tools & Code to Abuse WFP
- [Windivert](https://reqrypt.org/windivert.html): User-mode packet capture + injection
- [nfqnlk](https://github.com/cryptopunksju/nfqnlk): Full WFP injection lab
- Custom drivers (signed or sideloaded) can register WFP filters directly

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected WFP callouts or filters | Use `netsh wfp show filters` or ETW tracing |
| AV/EDR with internet but no logs | Alert when agent check-ins stop but process remains running |
| System DLLs communicating to attacker IPs | Watch for hijacked apps like `svchost.exe`, `teams.exe`, `runtimebroker.exe`

---

## 🔐 Mitigation Strategies
- Enable WFP auditing/logging with Microsoft-Windows-WFP provider
- Alert on unsigned or side-loaded drivers interacting with WFP
- Lock down driver installation via Secure Boot and Code Integrity
- Use firewall rules + EDR to monitor filter stacks and network providers

---

> 🧠 WFP abuse is **rootkit-grade stealth**. It doesn’t just hide malware — it hides *evidence* of malware. Real attackers don’t shut off the alarm — they rewire it to ignore them completely.
