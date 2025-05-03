# 🚨 GPU Escape in the Cloud – Breaking Isolation via Shared Devices

**GPU escape** attacks target virtualization flaws in shared cloud GPUs (NVIDIA, AMD, etc.), allowing an attacker to **break out of a guest VM** and gain access to the host or neighboring tenants. As GPUs are increasingly virtualized for ML workloads, **driver bugs, shared memory, and hypervisor gaps** become critical risks.

> ⚠️ This is not science fiction — these flaws have existed in NVIDIA vGPU, AMD MxGPU, and cloud hypervisors. Exploitation can lead to **VM-to-VM lateral movement**, host access, or cloud-wide compromise.

---

## 🛠️ What It Does
- Exploits flaws in how cloud GPU devices are shared across VMs:
  - Memory mapping bugs
  - DMA buffer leakage
  - GPU driver privilege escalation
- Breaks guest boundary via GPU **shared memory or driver calls**, escaping into host or accessing neighbor tenants’ data

---

## 💣 Attack Vectors

### 1. **vGPU Memory Leakage / Residue Reads**
- VM A releases GPU memory
- VM B allocated same VRAM region — reads leftovers from VM A
- Used to leak ML model weights, training data, or even secrets processed in GPU

### 2. **vGPU Device Escape (vmmcall / ioctl)**
- Vulnerabilities in ioctl handlers (e.g., NVIDIA CVE-2020-11487)
- Crafted command chain triggers out-of-bounds access in vGPU kernel module
- Leads to code execution in hypervisor context or escape to host kernel

### 3. **RDMA + GPU Chaining**
- Use RDMA to access remote GPU’s memory regions via weak ACL
- Bridge memory access across tenants or containers in ML clusters

---

## 🧪 Real-World Examples
| CVE | Description |
|-----|-------------|
| CVE-2020-11487 | NVIDIA vGPU escape via ioctl handler overflow |
| CVE-2022-21840 | Hyper-V GPU memory corruption RCE |
| CVE-2021-1074 | Priv-esc via NVIDIA kernel mode driver (Windows/Linux) |

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| GPU memory access outside expected tenant workload | Use GPU memory audit tools or vGPU isolation logs |
| Crashes in hypervisor GPU modules | Monitor dmesg/hypervisor logs for faults on device `/dev/nvidia*` or VFIO devices |
| vmmcall/syscall anomalies from guest | Audit guest syscall activity with elevated hooks or introspection

---

## 🔐 Mitigation Strategies
- Enforce strict GPU tenancy isolation — no shared VRAM without zeroing
- Update guest and host GPU drivers regularly
- Disable vGPU passthrough where not needed
- Apply strict ACLs and sandboxing for RDMA, VirtIO, and gRPC APIs
- Monitor for known attack signatures in GPU syscall usage (YARA for CUDA/OpenCL)

---

> 🧠 In the cloud, your neighbor’s GPU is your GPU — until someone escapes. And when the sandbox breaks, **your secrets are the prize.**

This is how shared silicon becomes **a shared vulnerability.**
