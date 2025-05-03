# 🧠 VM-Based Rootkits – Hypervisors Beneath the OS

**VM-based rootkits** use virtualization to gain full control over a target system by placing a lightweight **hypervisor below the operating system** — without the OS knowing. These stealthy implants can monitor, modify, and control system behavior invisibly, acting as **the invisible ring -1**.

> ⚠️ This is the concept behind **Blue Pill**, **SubVirt**, and advanced APT implants. Once the system boots inside a hidden VM, **everything you see is controlled by what you don’t.**

---

## 🛠️ What It Does
- Loads a malicious hypervisor beneath the OS using Intel VT-x or AMD-V
- Migrates running OS into a VM *on the fly* or boots it within one
- Rootkit lives in **Ring -1** — beneath Ring 0 (kernel), beyond EDR

---

## 💣 Attack Flow

### 1. Check for VT-x / AMD-V Support
```bash
cat /proc/cpuinfo | grep -E 'vmx|svm'
```
✅ Confirms the system supports virtualization extensions

### 2. Drop a Minimal Hypervisor
- Use `vmcall`, `vmx_on`, and `vmwrite` to launch custom hypervisor
- Trap syscalls, modify memory, introspect processes, or conceal activity

✅ OS believes it's on bare metal — but it's not

### 3. Monitor, Patch, or Hide
- Intercept kernel hooks, patch APIs, hide files/processes
- All done from below the OS, undetectable to most integrity scanners

---

## 🧪 Notable PoCs & Tools
| Tool | Use |
|------|-----|
| [Blue Pill](https://invisiblethings.org/papers/blue_pill.html) | Original dynamic VM rootkit concept |
| SubVirt | Malware-embedded hypervisor prototype (MSR/UMich) |
| HyperPlatform | Legit hypervisor framework that can be adapted |
| Bareflank | Open-source hypervisor for red team testing

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unexpected VM exits or nested paging | Analyze MSR registers and VMX control state |
| Hypervisor present when none expected | Use CPU feature check tools (`chkcpu`, `virt-what`) |
| Inconsistent timing (RDTSC drift) | Detect hypervisor time warping or delay injection

---

## 🔐 Mitigation Strategies
- Enforce firmware attestation (TPM + Secure Boot + DRTM)
- Disable hardware virtualization where unused
- Monitor for unexpected hypervisor activity or new CPU ID flags
- Treat BIOS/UEFI and bootloaders as high-integrity trust anchors

---

> 🧠 A VM rootkit is like building a **simulation beneath reality** — your OS runs fine, your apps run clean, but they’re no longer in control.

This is how attackers **redefine the machine underneath your machine.**
