# 🔓 Hardware Key Extraction – Cold Boot & Voltage Glitching Attacks

**Hardware key extraction attacks** allow adversaries to steal cryptographic secrets directly from hardware — even when software protections are airtight. These methods exploit how memory and CPUs behave under physical stress, power anomalies, or temperature shifts.

> ⚠️ These are real-world techniques used by researchers, governments, and APTs to bypass full-disk encryption, TPM protections, or secure boot keys — **without knowing passwords or credentials**.

---

## 🛠️ What It Does
- Targets hardware-level artifacts of key storage:
  - Residual charge in DRAM (cold boot)
  - Fault injection via voltage glitches
  - Side-channel reads from memory buses or cache
- Bypasses OS-level defenses entirely

---

## 💣 Key Attack Methods

### 1. **Cold Boot Attack**
- DRAM retains data for seconds after shutdown
- Freeze RAM chips (e.g., with compressed air)
- Move them to attacker-controlled system
- Dump memory to recover encryption keys

✅ Works against:
- BitLocker (if pre-boot auth is bypassed)
- LUKS/dm-crypt
- FileVault, VeraCrypt

### 2. **Voltage Glitching (Fault Injection)**
- Temporarily reduce or spike voltage to bypass logic (e.g., lockout checks)
- Often used on TPMs, secure elements, microcontrollers
- Bypass secure boot or extract secrets from flash/EEPROM

✅ Tools: ChipWhisperer, crowbarDFA, PicoEMP

### 3. **Bus Snooping or Side-Channel Reads**
- Use logic analyzers or EM probes to capture data from:
  - SPI/I2C buses (e.g., BIOS, TPM flash)
  - DDR lanes or CPU pin leakage

---

## 🧪 Proof-of-Concept: Cold Boot Recovery
```bash
# Dump memory image from frozen RAM
dd if=/dev/mem of=memdump.bin bs=1M
# Analyze for AES keys with volatility or rekall
```
✅ Can recover disk unlock keys, crypto material, kernel passwords

---

## 🕵️‍♂️ Detection & Forensics
| Indicator | Method |
|-----------|--------|
| RAM removed or thermal trace detected | Physical inspection, board tamper sensors |
| Boot without normal shutdown sequence | Firmware boot logs or secure boot events |
| Unexplained voltage drops/spikes | Power rail monitoring with oscilloscope or secure hardware logs

---

## 🔐 Mitigation Strategies
- Use FDE with **pre-boot authentication** (BitLocker PIN, passphrase)
- Enable TPM + Secure Boot + anti-tamper
- Fill memory with 0s on shutdown (`systemd-cryptsetup wipe`)
- Apply conformal coating or epoxy over RAM/TPM modules
- Power off completely (no sleep/suspend) when storing secure devices

---

> 🧠 Crypto is math. But keys live in **electricity, heat, and timing**. That’s where hardware attacks find them.

This is how secrets are stolen from **frozen chips, glitched logic, and silent buses.**
