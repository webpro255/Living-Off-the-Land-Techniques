# 🛡️ Intel BootGuard Bypass – Subverting Firmware Integrity at Boot

**Intel BootGuard** is designed to ensure that a system’s firmware (BIOS/UEFI) is cryptographically signed and verified before execution. When misconfigured, however, attackers can **bypass or disable BootGuard**, allowing execution of malicious firmware or unsigned UEFI drivers — while appearing “secure.”

> ⚠️ This technique has been abused by UEFI rootkits like **CosmicStrand** and **BlackLotus**, despite BootGuard being “enabled.” If the signing chain or platform fuses are broken, **persistence becomes invisible**.

---

## 🛠️ What It Does
- BootGuard verifies firmware integrity using an Intel-signed hash chain stored in **fuses and flash**
- If OEMs misconfigure the key fuses or disable enforcement in the ACM (Authenticated Code Module), attackers can:
  - Replace UEFI DXE drivers
  - Hook OS boot process invisibly
  - Maintain rootkit persistence under Secure Boot

---

## 💣 Attack Paths

### 1. **Platform Misconfiguration (ACM or Fuses)**
- OEM fails to burn BootGuard fuses correctly or ships with enforcement disabled
- Firmware can be rewritten **even if BootGuard appears active** in logs
- Used by real-world malware like CosmicStrand and MoonBounce

### 2. **Downgrade BootGuard Policy or ACM**
- Replace BIOS region with older BootGuard-disabled firmware image
- Exploit rollback or chain-of-trust weakness

### 3. **Signed Malware Modules via Stolen Keys**
- Use stolen OEM key to sign malicious UEFI module
- Passes BootGuard validation — persists below OS

---

## 🧪 Detection Tips
| Indicator | Method |
|-----------|--------|
| Inconsistent BootGuard enforcement | Compare MEInfo / FIT table vs. actual boot logs |
| UEFI modules with no vendor traceability | Inspect DXE drivers, hash against known-good modules |
| BootGuard set to Verified Boot instead of Measured Boot | Flag on endpoint BIOS security config

---

## 🔐 Mitigation Strategies
- Require BootGuard **Verified Boot** with properly fused OEM public keys
- Enable **BIOS Lock** and SPI flash protections
- Monitor for unauthorized firmware rewrites (e.g., SPI FlashGuard, CHIPSEC)
- Reflash system with known-good image if compromise suspected
- Audit ACM configuration with Intel tools (e.g., FITC, MEInfo)

---

> 🧠 BootGuard is only as strong as its fuse settings and signing chain. If it’s not fused, it’s not guarded. And that’s how malware boots before your OS — and Secure Boot says it’s fine.

This is how the rootkits *rise before Windows ever starts.*
