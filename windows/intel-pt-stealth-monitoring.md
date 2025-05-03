# 🧠 Intel PT Abuse – Stealth Monitoring via Processor Trace

**Intel Processor Trace (PT)** is a powerful CPU feature designed for performance profiling and debugging. Attackers can abuse it to perform **stealthy keylogging, syscall tracing, and memory access monitoring** — all with **zero footprint on disk or user space**.

> ⚠️ This is spy-tier surveillance. Intel PT can log every branch taken by a target process — silently, efficiently, and without using traditional hooks or callbacks that AV/EDR rely on.

---

## 🛠️ What It Does
- Intel PT captures **control flow data** (e.g., branches, jumps, calls, returns)
- Output is sent to memory buffers or dedicated PT hardware (e.g., ToPA)
- Attackers configure PT to trace a target process or kernel region
- With trace decoding, they reconstruct **executed instructions**, **syscalls**, or even infer **typed keystrokes**

---

## 💣 Attack Flow

### 1. Configure Intel PT on Target Core
- Use `perf_event_open()` (Linux) or `PTWConfig` (Windows/VMX-based)
- Set buffer base and size (e.g., 256 KB ring buffer)
- Attach to target PID or specific CR3

### 2. Start Tracing
```bash
perf record -e intel_pt//u -p <pid> -- sleep 10
```
✅ Captures microarchitectural trace for user-mode code execution

### 3. Decode Execution for Monitoring
```bash
perf script
```
✅ See instruction-level flow, identify syscall use, and infer input sequences

---

## 🕵️‍♂️ Offensive Uses
- **Keystroke logging** by reconstructing function call branches
- **Syscall and memory tracing** for credential dumping or stealth recon
- **Anti-debugging evasion** — no hooks, no injected code, no syscall interception
- **Process behavior profiling** to build AV/EDR-resistant malware

---

## 🧪 Tools & Frameworks
| Tool | Use |
|------|-----|
| `perf` (Linux) | Setup and decode Intel PT traces |
| `ptdecoder` | Instruction-level PT decoder (forensics + malware analysis) |
| `INTEL-PT-LIB` | Native Intel PT control API |
| Custom VMExit-based PT tracing | For red team stealth tooling

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Abnormal PT buffer activity | Monitor `perf_event_open` syscalls and unusual ToPA configs |
| PT drivers or VM exits during normal operation | Hypervisor-level trace audit |
| Consistent trace capture from sensitive processes | Alert on persistent userland `perf` sessions

---

## 🔐 Mitigation Strategies
- Restrict PT access to privileged users only
- Disable PT in BIOS/UEFI if unused
- Monitor `perf_event_open` calls or attach prevention policies (SELinux, AppArmor)
- Use secure enclaves or obfuscation to prevent full branch tracing

---

> 🧠 Intel PT is the **flight recorder of the CPU** — but in the wrong hands, it becomes an undetectable surveillance implant. It watches everything... but touches nothing.

This is how elite attackers **watch from beneath your debugger.**
