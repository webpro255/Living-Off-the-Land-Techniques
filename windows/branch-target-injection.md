# 🧠 Branch Target Injection – Poisoning the CPU's Mind

**Branch Target Injection (BTI)**, a class of **Spectre-style speculative execution attacks**, allows attackers to mis-train the CPU’s branch predictor to redirect speculative execution to attacker-controlled code gadgets. This transiently executes instructions that leak data through cache-based side channels — without violating memory access rules.

> ⚠️ This is not just a leak — it's *mind control for processors*. The attacker poisons prediction to trick the CPU into speculating on their terms.

---

## 🛠️ What It Does
- BTI tricks the CPU into *speculatively executing* code at an attacker-chosen location
- Exploits **indirect branch prediction** (e.g., `call rax`, `jmp rbx`) by training the Branch Target Buffer (BTB)
- The attacker plants or uses existing gadgets that leak secret data through side channels like cache timing

---

## 💣 Attack Flow

### 1. Train Branch Predictor
- Repeatedly execute indirect branches in attacker code to train BTB
- Predictor starts associating certain branch sources with chosen targets

### 2. Trigger Speculative Execution in Victim
- Victim executes similar indirect branch
- CPU mispredicts based on BTB poisoning and speculatively jumps to attacker gadget

### 3. Leak Data via Side Channel
- Gadget uses secret-dependent memory access to influence CPU cache
- Attacker measures timing (Flush+Reload, Prime+Probe) to recover secrets

---

## 🧪 Proof-of-Concept (Simplified Gadget)
```c
// Speculative gadget in victim binary
void gadget(unsigned long secret) {
    if (secret < array_size) {
        temp &= cache[array[secret] * 4096];
    }
}
```
✅ Attacker speculates this with a trained BTB and measures cache to recover `secret`

---

## 📉 Impact
- Leak kernel or process memory without system calls
- Cross-process, cross-VM, or sandbox bypass in browsers
- Breaks memory isolation at the **hardware prediction level**

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Repetitive indirect branch calls with unaligned memory access | Trace with PMCs or dynamic instrumentation (e.g., Intel PT) |
| High-resolution timers in unexpected code | Look for `rdtsc`, `rdtscp`, or timing loops |
| Spectre-specific syscall patterns | Monitor for unusual BTB training sequences

---

## 🔐 Mitigation Strategies
- Apply CPU microcode updates enabling **IBRS / IBPB / STIBP** (Spectre mitigations)
- Use **retpoline**-compiled binaries to block indirect branch speculation
- Disable untrusted JIT in browser sandboxes (or use Site Isolation)
- Constant-time code for crypto operations

---

> 🧠 Branch Target Injection shows the CPU doesn’t just execute — it guesses. And when the guesses are poisoned, **speculation becomes a spy.**

This is speculative betrayal — and it happens before permission is ever checked.
