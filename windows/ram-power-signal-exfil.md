# 🔋 Signal Reflections from RAM Power Draw – Data Exfiltration via EM Power Lines

**Signal reflection attacks from RAM power draw** leverage fluctuations in the power supply lines of DRAM modules to reconstruct memory access patterns or sensitive data. These attacks can be conducted without touching the machine — by placing sensors near power lines or measuring induced emissions.

> ⚠️ RAM speaks — not in bytes, but in **voltage ripples**. And when attackers listen carefully, they hear secrets flow.

---

## 🛠️ What It Does
- DRAM access generates distinctive power fluctuations
- These can be picked up via:
  - Physical probes on motherboard power rails
  - Nearby electromagnetic (EM) sniffing devices
  - Power line coupling via shared circuits (aka **PowerHammer**)
- The patterns correlate with memory access, revealing:
  - Keystroke timing and codes
  - Crypto key operations
  - Application activity (page hits, loading behavior)

---

## 💣 Attack Techniques

### 1. **Onboard Sensor + Logic Analyzer**
- Attach probes to DRAM power rail (VDD)
- Feed into oscilloscope or SDR to decode access patterns

### 2. **Power Line Injection/Reflection (PowerHammer Variant)**
- Malicious software modulates CPU/GPU/RAM usage to send binary patterns over power lines
- Receiver picks up via external power circuit tapping

### 3. **EM Leakage from RAM Chips**
- Place EM sensors (coil, SDR) close to laptop or DIMM sockets
- Decode changes in activity by observing EM flux

---

## 🧪 Indicators of Attack
| Signal | Detection Method |
|--------|------------------|
| Highly regular RAM activity during idle state | Hardware Performance Counters (HPCs) or power analytics tools |
| Unexpected load changes on PSU rails | Oscilloscope or smart PSU monitoring |
| EM flux around DIMMs during passive observation | RF shielding sweep or portable EM scanner

---

## 🔐 Mitigation Strategies
- Shield RAM power lines and apply filtering capacitors
- Use EM shielding for devices in secure zones (TEMPEST-grade enclosures)
- Monitor PSU noise and correlate with expected load profiles
- Implement constant-time memory access and load randomization for cryptographic operations

---

> 🧠 Your RAM doesn’t just store secrets — it **broadcasts its workload** with every draw of power.

This is side-channel espionage **without malware, without code — just signal.**
