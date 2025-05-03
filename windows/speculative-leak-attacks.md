# ⚙️ Speculative Execution Attacks – Leaking Secrets via the CPU

**Speculative execution attacks** exploit how modern CPUs try to execute instructions *ahead of time* to improve performance. These predictions can cause data from protected memory to be temporarily loaded into CPU caches — where attackers can steal it using timing side-channels.

> ⚠️ These are real-world, high-impact attacks like **Spectre, Meltdown, ZombieLoad, Fallout, and Downfall**. They don’t break crypto — they break the CPU’s promise to keep secrets safe.

---

## 🛠️ What It Does
- CPU speculatively executes instructions **before knowing if they’re allowed**
- Data from unauthorized memory access may be loaded into cache
- If prediction was wrong, results are discarded — but the cache remains changed
- Attackers use **timing attacks** (like Flush+Reload or Prime+Probe) to read what was accessed

---

## 💣 Key Exploit Families

### 1. **Spectre (Variants 1/2/4/RSB)**
- Manipulates branch prediction to speculatively read arbitrary memory
- Targets kernel→user, sandbox→host, or even VM→VM boundaries

### 2. **Meltdown**
- Exploits CPU race condition to read kernel memory from user space
- Affected older Intel CPUs — patched via KAISER/KPTI

### 3. **ZombieLoad (MDS)**
- Leaks data from CPU buffers shared between cores/threads
- Even secrets from other processes and VMs

### 4. **Downfall (GDS, 2023)**
- Leaks vector register state across instructions and privilege boundaries
- Targets AVX instructions (Intel processors)

---

## 🧪 Lab Example: Flush+Reload Timing Attack
```c
// Simplified example — measure if data is in cache
start = rdtsc();
access(some_address);
end = rdtsc();
if (end - start < threshold) {
  // Data was cached — leaked!
}
```
✅ Can be used to leak keystrokes, RSA keys, AES tables, or memory contents

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Frequent cache flush/invalidate syscalls | Monitor `clflush` patterns or suspicious timing loops |
| Cross-process timing measurements | Look for high-res timers and evasion of rdtsc traps |
| Unusual transient faults in microbenchmarks | Use CPU fuzzers or firmware-level integrity tools

---

## 🔐 Mitigation Strategies
- Apply CPU microcode and OS-level patches (e.g., Spectre/Meltdown mitigations)
- Disable SMT (Simultaneous Multi-Threading) in high-security systems
- Use retpolines, L1D flushes, and serializing instructions in sensitive code
- Monitor for speculative attack patterns in cloud or shared environments

---

> 🧠 These aren’t software bugs — they’re **flaws in how silicon handles trust**. You didn’t run malicious code… but speculative execution *did it anyway*.

Welcome to the era where **timing is your enemy — and your CPU is the snitch.**
