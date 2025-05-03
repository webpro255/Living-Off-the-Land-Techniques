# 🎮 GPU Memory Persistence – Malware in the Shadows of VRAM

**GPU memory persistence** is an advanced technique where attackers store payloads, implants, or shellcode in **graphics card memory (VRAM)** to avoid detection and forensic tools. Since VRAM is outside the scope of most antivirus and EDR systems, it acts as a stealth data stash or staging area.

> ⚠️ VRAM is unmanaged, rarely scanned, and not zeroed between allocations. Attackers can hide code in plain sight — especially using CUDA or OpenCL APIs.

---

## 🛠️ What It Does
- Allocates memory buffers on the GPU using CUDA/OpenCL APIs
- Loads payloads into GPU memory, avoiding system RAM and disk
- Optionally executes GPU-based computation or uses it as a **covert staging buffer** for code later copied back to RAM and executed

---

## 💣 Attack Flow

### 1. Allocate VRAM Buffer (CUDA Example)
```c
cudaMalloc((void **)&gpu_buffer, buffer_size);
cudaMemcpy(gpu_buffer, payload, buffer_size, cudaMemcpyHostToDevice);
```
✅ Shellcode is now stored in GPU — invisible to most host-based scanners

### 2. Retrieve/Execute Payload Later
- Malware reads back buffer from VRAM
```c
cudaMemcpy(host_buffer, gpu_buffer, size, cudaMemcpyDeviceToHost);
execute_payload(host_buffer);
```
✅ Persistence achieved without ever writing to disk or system memory long-term

### 3. Optional: Use GPU for Obfuscated Logic
- Run malicious logic in CUDA kernel or OpenCL task
- Exfiltrate computation result or beacon via GPU-side covert channel

---

## 🧪 Tools / PoCs
| Tool | Use |
|------|-----|
| [WIN64/GPUstager](https://github.com/ekknod/GPUstager) | Shellcode in VRAM (Windows) |
| [PyCUDA](https://documen.tician.de/pycuda/) | Python interface for GPU memory management |
| [GPU-based keyloggers (PoC)] | Log input events via DMA sharing (theoretical)

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Unusual GPU memory usage by non-GPU apps | Monitor CUDA/OpenCL calls with runtime hooks |
| Host-device memory transfers without GPU workloads | Correlate GPU activity with normal rendering/compute behavior |
| Abnormal VRAM usage patterns | Use GPU driver introspection tools (e.g., NVIDIA Nsight, AMD ROCm)

---

## 🔐 Mitigation Strategies
- Limit access to GPU APIs (CUDA/OpenCL) via driver or group policy
- Monitor GPU driver usage with system audit tools
- Use VRAM zeroing and runtime kernel memory scrubbing on shutdown/reboot
- Audit GPU memory state using debug tools in high-security environments

---

> 🧠 VRAM is the **dark side of memory** — big, fast, and usually ignored. That’s why it’s perfect for payloads that **need to hide in motion.**

This is where malware waits between frames. Welcome to GPU-based persistence.
