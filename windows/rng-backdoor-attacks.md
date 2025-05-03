# 🎲 RNG Backdoor Attacks – Subverting Entropy at the Source

**Random Number Generators (RNGs)** are foundational to cryptographic security — used in key generation, session tokens, password salting, and more. When attackers **backdoor the RNG**, they break the entire system without touching anything else. It's the ultimate silent compromise.

> ⚠️ This was allegedly done in the real world via **NSA’s Dual_EC_DRBG** algorithm. Backdooring RNGs lets attackers predict “random” keys, tokens, and secrets across entire fleets.

---

## 🛠️ What It Does
- A weak or rigged RNG can appear normal to all audits, but its output is **predictable** to the attacker
- Backdoored RNGs allow attackers to:
  - Reproduce private keys if they observe the public key
  - Crack TLS/SSH sessions in transit
  - Bypass encryption, even on air-gapped machines

---

## 💣 Attack Techniques

### 1. Dual_EC_DRBG (Real-World NSA Backdoor)
- A standardized RNG algorithm based on elliptic curves
- If attacker knows the secret curve parameters used to generate it, **they can predict all output**
- Used in RSA’s BSAFE crypto library — paid millions by NSA to include it

### 2. Subverting `/dev/random` or RDRAND (Linux/Intel)
- Modify entropy sources in Linux kernel
- Hook or replace hardware RNG instructions (e.g., Intel RDRAND)
- Malware injects predictable entropy or feeds poisoned values into RNG pool

### 3. RNG Injection in Virtual Machines / Cloud
- Seed multiple VMs with same predictable entropy (e.g., timestamp + hostname)
- Predict SSH keys, JWTs, or session tokens across the fleet

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Entropy pool anomalies | Kernel-level entropy monitors (e.g., `/proc/sys/kernel/random/entropy_avail`)
| Identical or low-variance key generation across systems | Audit SSH or TLS keys for pattern reuse
| Use of deprecated/legacy RNGs (e.g., Dual_EC_DRBG, ANSI X9.31) | Static crypto analysis tools (e.g., `CryptoLint`, `RNGAudit`)

---

## 🔐 Mitigation Strategies
- Use high-entropy, audited RNGs (e.g., ChaCha20, Fortuna, /dev/urandom)
- Avoid hardware-only RNGs (e.g., RDRAND) without mixing with software entropy
- Require entropy hardening in CI/CD pipelines
- For critical systems: perform cryptographic output randomness testing

---

> 🧠 If crypto is the lock, RNG is the keymaker. A compromised RNG doesn’t crack encryption — **it makes encryption irrelevant.**

The right backdoor in the wrong number changes everything.
