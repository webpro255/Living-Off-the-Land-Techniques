# 🧱 SGX Enclave Persistence – Trusted Execution Gone Rogue

**Intel SGX (Software Guard Extensions)** allows the creation of **Trusted Execution Environments (TEEs)** — isolated memory regions encrypted by the CPU. Attackers can abuse SGX to hide payloads, encryption keys, or full malware **inside the enclave**, completely invisible to OS, AV, EDR, or hypervisors.

> ⚠️ SGX is designed to be tamper-proof — and that includes defenders. Once malware enters an enclave, it becomes *unkillable and unseeable* without cooperation from the CPU vendor. This is persistence for ghosts.

---

## 🛠️ What It Does
- SGX enclaves run encrypted code in isolated memory regions
- Memory is decrypted only **inside the CPU** — not visible in RAM dumps
- Used legitimately for DRM, financial security, password vaults
- Abused, it becomes **a stealth implant container**

---

## 💣 Attack Flow

### 1. Create a Malicious Enclave
- Use Intel SGX SDK to build an enclave that:
  - Stores encrypted payload or logic
  - Performs crypto, key handling, or memory patching from inside enclave
- Deliver enclave as part of a signed or trojanized app

### 2. Load Enclave from Dropper or Loader
```c
sgx_create_enclave("malicious.signed.so", &enclave_id);
```
✅ Enclave loads into protected memory — only accessible through enclave APIs

### 3. Trigger Logic Internally
- Command/control logic, obfuscated crypto, or staged payloads reside inside
- Execution controlled via ECALL/OCALL interface

### 4. Persistence
- Loader app respawns enclave at boot
- Payload is only ever decrypted inside SGX
- Optional: Download full payload via encrypted C2, decrypt inside enclave

---

## 🕵️‍♂️ Detection Challenges
| Indicator | Method |
|-----------|--------|
| SGX enclave memory not visible | RAM dumps will show encrypted junk |
| API usage of `sgx_create_enclave` | Static/dynamic inspection of binaries using SGX SDK |
| Long-running enclave-based processes | Monitor enclave runtime footprint (very low visibility)

---

## 🔐 Mitigation Strategies
- Disable SGX in BIOS/UEFI if unused
- Monitor apps using SGX SDK or ECALL interfaces
- For ultra-secure environments: require enclave attestation + audit enclave code
- Restrict user-mode access to SGX runtime APIs

---

> 🧠 SGX was made to protect secrets — even from your OS. But once the wrong code runs inside, **that protection becomes a fortress for malware**. And no scanner will ever find it.

If persistence is about hiding in plain sight, **SGX is the vault no one can open.**
