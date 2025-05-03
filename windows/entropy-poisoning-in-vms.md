# 🧬 Entropy Poisoning in VMs – Breaking Crypto from the Inside

**Entropy poisoning** in virtual machines refers to attacks where low-quality or malicious entropy is injected into a system’s random number generator (RNG), weakening cryptographic operations like key generation, session tokens, and TLS handshakes — often **without detection**.

> ⚠️ In multi-tenant cloud or virtualized environments, shared entropy sources or poorly seeded RNGs can lead to **predictable crypto** across machines. Entropy poisoning is the silent killer of trust.

---

## 🛠️ What It Does
- Seeds a virtual machine’s RNG with **low-entropy or predictable values**
- Allows attacker to:
  - Predict SSH, JWT, or TLS session keys
  - Cause duplicate or weak cryptographic material
  - Leak secrets across cloned/mirrored VM instances

---

## 💣 Attack Paths

### 1. **Cloned VMs with Identical Entropy State**
- Snapshot-based cloud VM images can reuse RNG state
- Leads to duplicate SSH keys or predictable session tokens

### 2. **Malicious Entropy Injection in Boot Process**
- Poison `/dev/random` or kernel entropy pool during early init
- Done via initramfs, malicious drivers, or side-channel guest interaction

### 3. **Entropy Side-Channels Between Guests**
- Cross-VM leakage via timing, shared virtual hardware, or CPU RNGs (e.g., RDRAND)
- Entropy starvation on host causes correlated key generation

---

## 🧪 Proof-of-Concept: Weak SSH Key Generation
```bash
# In a poisoned VM:
rm -f /dev/random && ln -s /dev/zero /dev/random
ssh-keygen -f weak_key
```
✅ Resulting key is deterministic and easily brute-forceable

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Identical SSH/TLS keys across VMs | Audit keys during provisioning (cloud-scale scanning) |
| Low-entropy randomness in boot | Trace entropy init sources in initramfs and bootloader logs |
| Repeated values in crypto material | Use key uniqueness scanners and fuzzers (e.g., `rnp`, `entropy-checker`)

---

## 🔐 Mitigation Strategies
- Use independent hardware RNG (e.g., virtio-rng backed by host TPM)
- Inject entropy from trusted host source on every boot
- Enable jitter entropy or `haveged` early in boot
- Enforce post-boot key generation only after RNG pool is verified healthy
- Use `rngd` or kernel entropy health checks (Linux: `random.boot_id`)

---

> 🧠 RNG defines crypto — and crypto defines trust. If you share entropy, **you share secrets**. And poisoned entropy means **everything secure is now guessable.**

This is **predictable randomness** — and it’s how clouds leak keys.
