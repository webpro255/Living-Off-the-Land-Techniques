# 📡 TEMPEXT – Electromagnetic Exfiltration via EM Side Channels

**TEMPEST-style EM exfiltration** (aka TEMPEXT) refers to leaking data from air-gapped systems using unintended **electromagnetic emissions**. Attackers exploit electrical activity from monitors, CPUs, keyboards, or buses to create radio-frequency (RF) signals that can be captured by nearby receivers — even **through walls or without network access**.

> ⚠️ This is real — demonstrated by NSA documents and academic research. The machine “leaks” RF every time it computes. With the right antenna, **data escapes silently.**

---

## 🛠️ What It Does
- Uses electrical transitions in circuits to emit RF signals
- Common emitters: VGA cables, HDMI, power supplies, DRAM, keyboard matrices
- Leaked RF can encode:
  - Typed keystrokes
  - Pixel data (e.g., exfiltrate text via screen refresh)
  - Memory access patterns

---

## 💣 Attack Techniques

### 1. **Monitor Cable RF Emissions (VAN ECK Phreaking)**
- VGA or DVI signals emit unshielded RF
- Receiver with SDR decodes screen contents from RF leakage
- Range: Up to 10–20 meters with directional antenna

### 2. **Keystroke Eavesdropping via EM Leakage**
- Keyboard scan matrix emits unique EM patterns per key
- Train model on signal fingerprints to recover input

### 3. **CPU or DRAM Modulation (AirHopper / ODINI)**
- Control memory bus or CPU activity to modulate data into EM signal
- Requires malware to run tight loops or specific instructions
- SDR or AM radio picks up the pattern

---

## 📡 Equipment Used
| Tool | Purpose |
|------|---------|
| RTL-SDR / HackRF / LimeSDR | EM signal capture & demodulation |
| Directional antenna | Increase range & precision |
| GNURadio / SDR# | Signal processing & decoding |
| EM leakage simulation: TEMPEST lab or Faraday room |

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| High CPU activity in idle or air-gapped mode | Process monitoring or power analysis |
| SDR shows unexpected emissions at known frequencies | Signal sweeps with spectrum analyzer |
| EM signal correlates with typing or screen updates | Correlation tests using dual instrumentation

---

## 🔐 Mitigation Strategies
- Shielded cables and TEMPEST-rated devices
- Faraday cages or RF-isolated rooms
- Disable unused video outputs or internal monitors in secure zones
- EM activity monitoring in classified environments

---

> 🧠 You didn’t connect to Wi-Fi. You didn’t plug in a drive. But the machine **spoke** — and someone was listening.

This is cyber espionage by waveform. **TEMPEXT proves even silence emits data.**
