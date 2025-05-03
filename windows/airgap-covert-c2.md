# 🌑 Air-Gap Covert C2 – Exfiltration Without a Network

**Air-gap covert channels** allow attackers to communicate with and exfiltrate data from isolated systems that have **no network, Bluetooth, or USB access**. These methods abuse **physical emissions** like light, sound, heat, vibration, or electromagnetic signals to send data silently — bit by bit.

> ⚠️ These are used by elite APTs and researchers. You don’t need a connection — just a **leak**. From blinking LEDs to fan noise, every component is a potential exfil vector.

---

## 🛠️ What It Does
- Exploits physical side effects of computing devices:
  - Monitor brightness
  - HDD LED flashes
  - CPU heat patterns
  - Fan speed
  - Acoustic signals
  - Power fluctuations
- Attacker places a **receiver** nearby (camera, smartphone, radio antenna) to decode leaked data

---

## 💣 Notable Techniques

### 1. **Optical Exfiltration via Monitor Brightness**
- Modulate screen pixels in imperceptible ways (low-intensity flicker)
- Use camera or light sensor to record changes
- Up to 100 bps with modern webcams

### 2. **HDD LED Blinking (LED-it-GO)**
- Flash HDD activity LED in binary sequence
- External camera records and decodes pattern

### 3. **Fan Noise Modulation (Fansmitter)**
- Modulate CPU/GPU fan speed to create acoustic patterns
- Captured by nearby microphone (phone, smartwatch)

### 4. **PowerLine Fluctuations (PowerHammer)**
- Modulate power consumption patterns via CPU load
- Attacker taps into building's electrical wiring to capture signal

### 5. **Ultrasonic Audio (MOSQUITO / Speak(a)r)**
- Repurpose speakers or microphones to emit/receive data in ultrasonic range
- Transmit data across air-gaps via audio (15–20 kHz+)

---

## 🧪 Receiver Toolchain
| Tool | Use |
|------|-----|
| Smartphone camera | Optical / LED-based channel receiver |
| SDR (RTL-SDR, HackRF) | EM or powerline exfil decoding |
| Microphone + FFT | Acoustic or ultrasonic decoding |
| Thermal cam / IR sensor | Heat pattern recognition

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Abnormal LED activity | Monitor HDD/Network LED rates over time |
| Fan RPM patterns | BIOS-level or motherboard logging for fan speed anomalies |
| Powerline noise | EMI filters or smart PDU anomaly detection |
| Acoustic emissions | Spectrum analysis of ambient noise

---

## 🔐 Mitigation Strategies
- Physically cover or disable LED indicators
- Lock BIOS fan curves, disable CPU thermal modulation
- Remove speakers/mics from air-gapped systems
- Isolate power lines or use EMI filters in sensitive areas
- Shielded rooms for true air-gap defense

---

> 🧠 You disconnected the network, removed the ports, sealed the USBs… and still lost the data. Air-gap C2 proves **everything leaks** — if you know how to listen.

Welcome to silent exfil — where **heat, light, and sound are your enemy.**
