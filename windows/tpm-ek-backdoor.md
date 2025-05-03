# 🧬 Backdooring TPM Root Keys – Fabrication-Time EK Compromise

**Trusted Platform Modules (TPMs)** rely on unique, embedded cryptographic keys called **Endorsement Keys (EKs)** that form the hardware root of trust. If an attacker compromises these keys — especially **during chip fabrication** — they can silently subvert all cryptographic operations, remote attestations, and secure storage forever.

> ⚠️ This is the ultimate hardware backdoor. If you own the EK, **you own the trust of the system** — invisibly, permanently, and without triggering alerts.

---

## 🛠️ What It Does
- Every TPM has a vendor-generated **EK pair** (usually RSA/ECC), burned into the chip at the factory
- Remote services use the EK public key to verify that a TPM is authentic and untampered
- If an attacker inserts a **known EK** or leaks EK private keys, they can:
  - Decrypt sealed storage
  - Forge TPM signatures
  - Falsify attestation reports
  - Clone TPM identity across systems

---

## 💣 Attack Methods

### 1. **Insider at Fabrication Plant or Supply Chain**
- Modify TPM firmware or OTP (One-Time Programmable) fuses to inject attacker-controlled EK
- Embed hidden backdoor logic in silicon using hardware trojans

### 2. **Leaked or Weak EK Generation (Historical)**
- Use poor random number generation to create predictable EKs
- Extract EK private keys during insecure provisioning (via I2C/SPI sniffing)

### 3. **Fake TPM with Valid EK Certificate**
- Clone TPM identity and issue fake EK cert from compromised CA
- Trusted computing systems accept attacker as legit TPM device

---

## 🧪 Detection is Nearly Impossible
| Technique | Feasibility |
|----------|-------------|
| Compare EK pubkey to known-good hash from OEM | ❌ Rarely available
| Check TPM against cloud attestation platform | ❌ Trust still assumed based on EK cert
| Scan for duplicate EKs across systems | ✅ Possible in forensic studies or fleet-wide audits

---

## 🔐 Mitigation Strategies
- Only source TPMs from **audited, trusted supply chains**
- Use **remote attestation with DRTM + PCR validation** — not just EK checks
- Log and verify all EKs in secure databases for later correlation
- Treat EKs as **revocable identities** — support EK revocation lists in cloud attestation platforms

---

## 🚨 Impact
- Complete compromise of sealed secrets
- Remote access to encrypted volumes
- Trust hijack of measured boot, credential guard, BitLocker, etc.
- Undetectable by OS, AV, or TPM software tools

---

> 🧠 This is trust subversion **at the atomic level of your hardware.** No patch. No forensics. No alert.

This is how attackers **own the root of trust** — not by breaking it, but by *being* it.
