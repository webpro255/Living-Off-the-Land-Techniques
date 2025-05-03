# 🧾 EV Signature Abuse – Weaponizing Trust in Code Signing

**Extended Validation (EV) certificates** are meant to prove that a binary is signed by a verified, trusted entity. But when attackers steal, buy, or abuse these certs, they can sign malware that bypasses SmartScreen, EDR, and even some allowlists — because the system trusts the **signature, not the code.**

> ⚠️ This is how malware looks legit, installs cleanly, and gets past corporate defenses. If the signature is trusted, **no one looks twice.**

---

## 🛠️ What It Does
- EV certificates are issued after strict verification of a company’s identity
- When stolen or sold (via dark web or insider abuse), attackers can:
  - Sign droppers and backdoors
  - Bypass Windows SmartScreen warnings
  - Appear as valid to EDRs that whitelist signed binaries

---

## 💣 Attack Paths

### 1. **Stolen EV Certificates**
- Attacker compromises a developer or build machine and exfiltrates `.pfx` cert + password
- Or extracts certs from poorly secured CI/CD systems

### 2. **Purchased or Faked Company**
- Attacker registers a shell company, passes EV validation process
- Receives legitimate cert from trusted CA (e.g., DigiCert, Sectigo)
- Signs malware as a seemingly legit vendor

### 3. **Signed Dropper with In-Memory Payload**
- EV-signed binary drops or loads payload into memory
- AV/EDR may allow execution due to the trusted signature

---

## 🧪 Detection Tips
| Indicator | Method |
|-----------|--------|
| EV-signed binaries from unknown or shell vendors | Monitor and validate against known vendor list |
| High-prevalence signature reuse across malware samples | Use YARA/Sigma to flag identical certificates |
| Signed binary exhibits malicious behavior post-launch | Apply runtime behavior analysis regardless of signature status

---

## 🔐 Mitigation Strategies
- Never trust a binary based solely on its signature — validate behavior too
- Revoke EV certs immediately if compromised (notify CA)
- Monitor signed file provenance in threat intel feeds
- Apply EDR policies to inspect signed binaries as deeply as unsigned ones

---

> 🧠 A signature is just a mask. EV abuse proves that **you can’t trust the cert — only the intent.**

This is how attackers **borrow your reputation to sneak in the front door.**
