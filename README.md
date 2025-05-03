# Living Off the Land Techniques ✨

This repository is a curated guide and lab for **Living Off the Land (LotL)** techniques used by hackers, red teamers, and advanced persistent threats (APTs). These methods abuse **legitimate binaries and built-in system features** to perform attacks *without downloading malware*.

> 🔒 **For Educational Use Only** — Every technique in this repo is for learning, defense, and ethical research. Don't be reckless.

---

## 🧵 What is "Living Off the Land"?
**LotL** is the art of using what already exists on a system to execute attacks. No need to bring your own tools — use native ones like `powershell`, `mshta`, `wmic`, `certutil`, and `schtasks`.

These methods:
- Avoid detection by AV/EDR
- Look like normal activity
- Are already trusted and signed by the OS

---

## 🚀 Getting Started
Check out these technique pages to see how each native tool can be abused.

### 💻 Windows Techniques
| Tool | Purpose |
|------|---------|
| [`powershell.md`](windows/powershell.md) | Execute payloads in memory, script downloaders |
| [`certutil.md`](windows/certutil.md) | Download payloads over HTTP/S silently |
| [`mshta.md`](windows/mshta.md) | Run remote HTA/JS payloads |
| [`rundll32.md`](windows/rundll32.md) | Execute scripts and DLLs |
| [`bitsadmin.md`](windows/bitsadmin.md) | Transfer files quietly in the background |
| [`wmic.md`](windows/wmic.md) | Remote process control, persistence |
| [`schtasks.md`](windows/schtasks.md) | Create persistent scheduled tasks |

### 🌬️ Linux / macOS Techniques
| Tool | Purpose |
|------|---------|
| [`bash.md`](linux/bash.md) | Command injection, script execution |
| [`crontab.md`](linux/crontab.md) | Persistence via scheduled jobs |
| [`curl.md`](linux/curl.md) | File download and C2 beaconing |
| [`ssh.md`](linux/ssh.md) | Lateral movement, key-based persistence |
| [`systemd.md`](linux/systemd.md) | Service-level persistence |
| [`osascript.md`](macos/osascript.md) | AppleScript-based execution |

---

## 🤖 Labs & Exercises
| Lab | Description |
|-----|-------------|
| [`lab1-basic-download.md`](labs/lab1-basic-download.md) | Use `certutil` + `mshta` to drop and execute a remote payload |
| [`lab2-c2-channel.md`](labs/lab2-c2-channel.md) | Build a C2 channel using GitHub issues or Discord webhooks |
| [`lab3-persistence.md`](labs/lab3-persistence.md) | Use `schtasks` and `wmic` to maintain access |

---

## 🕵️‍♂️ Defender Notes
| Topic | File |
|-------|------|
| How to detect LotL | [`how-to-detect-lotl.md`](defender-notes/how-to-detect-lotl.md) |
| Windows Event IDs | Coming soon |
| Sigma/YARA rules | Coming soon |

---

## 📆 Roadmap
- [x] Create base structure for common LotL tools
- [ ] Add Linux persistence examples
- [ ] Add C2 over GitHub walkthrough
- [ ] Add obfuscation tricks + detection rules

---

## ⚠️ Disclaimer
This repo is for **defenders, students, and researchers**. Use these techniques in labs, CTFs, and red team exercises **only**. 

> Unauthorized use = jail time. Respect the craft, respect the rules.

---

## 📅 Contribute
Have your own technique or variant? PRs welcome.
Just follow the format and label clearly what the command does, why it's dangerous, and how to defend against it.

---

Stay sharp. Stay stealthy. 
**Welcome to the land.**
