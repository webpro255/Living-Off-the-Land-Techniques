# 🧬 Intel TXT Fake Attestation – Spoofing the Chain of Trust

**Intel Trusted Execution Technology (TXT)** and **Dynamic Root of Trust for Measurement (DRTM)** are designed to launch a secure system environment by measuring the firmware, bootloader, and kernel at boot. But if the measurements or event logs can be spoofed, an attacker can **fake trust** — booting a malicious system while claiming it’s clean.

> ⚠️ TXT and DRTM don’t enforce — they report. If you can fake the report, **you can lie to the security stack**, the cloud, and remote verifiers.

---

## 🛠️ What It Does
- Intel TXT creates a Measured Launch Environment (MLE) using a special instruction (`GETSEC[SENTER]`) that logs hashes of BIOS/bootloader/kernel into TPM
- Remote attestation tools verify those hashes to confirm a system booted securely
- If the attacker:
  - Controls the early boot path
  - Patches the `SINIT` module or logs
  - Emulates TPM or modifies PCR logs

… then they can convince remote systems that an **untrusted system is trusted**

---

## 💣 Attack Techniques

### 1. **Tamper PCR Logs (IMA + TPM Event Log)**
- Patch or replay a known-good event log
- Modify TPM’s PCR registers (via emulation or transient override)
- Remote attestation passes, despite malicious boot chain

### 2. **Inject Malicious MLE with Valid Measurements**
- Load a rootkit/kernel that has been pre-hashed and signed (valid PCRs)
- Leaks data or backdoors the OS while presenting valid attestation

### 3. **DRTM Subversion (e.g., SENTER Abuse)**
- Use `GETSEC[SENTER]` to launch a fake secure environment
- Hijack bootloader to lie about boot state or memory ranges

---

## 🧪 Detection Tips
| Indicator | Method |
|-----------|--------|
| PCR logs mismatch runtime code hash | Compare PCR log entries with real-time memory dumps |
| Unexpected `GETSEC[SENTER]` or `SKINIT` usage | Monitor for SENTER invocations during suspicious boot sequences |
| Repeated attestation with identical PCRs | Flag replay attacks — PCRs should vary per boot

---

## 🔐 Mitigation Strategies
- Use TPM 2.0 with remote attestation that validates runtime behavior (not just boot logs)
- Compare PCR logs with real memory hashes at verification endpoint
- Secure and verify `SINIT` modules via Intel-provided hash lists
- Monitor for boot path tampering using `chipsec`, `tpm2-tools`, or boot attestation services

---

> 🧠 Trusted boot only works when the trust chain is **verifiable and enforced**. TXT without enforcement is just a fancy way to **say "trust me" — with no proof.**

This is how attackers lie to the chain of trust — **with forged proof and stolen reputation.**
