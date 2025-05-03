# 🔐 TPM NVRAM Backdoor - Hiding in Trusted Hardware

**TPM NVRAM backdoors** exploit the **non-volatile memory (NVRAM)** of Trusted Platform Modules (TPMs) to store hidden payloads or encryption keys. The data persists across reboots and OS reinstalls, and the TPM is rarely monitored — making this a perfect stealth implant location.

> ⚠️ This is rarely explored territory. The TPM is trusted by design — so attackers using it as covert storage essentially turn a security chip into a **hidden vault for malware or secrets**.

---

## 🛠️ What It Does
- TPMs have multiple **NVRAM indexes** — persistent storage areas used for secure key storage and measurements
- Attackers can:
  - Write to unused indexes (if unprotected)
  - Store config data, payload fragments, backdoor keys
  - Reload that data silently at boot or via malware component

---

## 💣 Attack Flow

### 1. Identify Writable NVRAM Indexes
```bash
tpm2_nvlist
```
✅ Lists all defined NVRAM slots

### 2. Define or Hijack a Slot
```bash
tpm2_nvdefine 0x1500016 -C o -s 64 -a "ownerread|ownerwrite"
```
✅ Creates an NVRAM index with read/write access by owner auth

### 3. Write Stealth Data
```bash
tpm2_nvwrite 0x1500016 -C o -i ./payload.bin
```
✅ Payload fragment, encryption keys, or C2 config is now in TPM — invisible to disk forensics

### 4. Read Later for Use
```bash
tpm2_nvread 0x1500016 -C o -o payload.bin
```
✅ Malware can reconstruct a config, unlock a backdoor, or rehydrate shellcode from TPM silently

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unusual or undocumented NVRAM indexes | Use `tpm2_nvlist` and validate against known secure boot indexes |
| Frequent reads from NVRAM | TPM audit logging or kernel tracing (not standard on most endpoints) |
| Indexes defined with owner-write | Flag anything outside of Secure Boot and BitLocker ranges

---

## 🔐 Mitigation Strategies
- Lock down TPM NVRAM indexes via platform firmware
- Clear or reset TPM on decommission or incident response
- Restrict userland TPM access (`tss2`, `tpmrm0`) to trusted processes only
- Implement TPM command monitoring in high-security environments

---

> 🧠 The TPM is meant to protect you — but if an attacker gets there first, it becomes their vault. They’re not stealing your secrets... they’re storing **their own.**

You looked at the TPM and saw trust. They saw free storage.
