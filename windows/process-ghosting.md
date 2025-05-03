# 👻 Process Ghosting - Fileless Execution Without Touching Disk

**Process Ghosting** is a cutting-edge technique that allows attackers to execute code **without ever writing a runnable file to disk**. Instead, they write to a file that is deleted before execution — bypassing most AV and EDR file-based detections.

> ⚠️ Stealthier than reflective injection or classic process hollowing. Used by malware like **HermeticWiper** and custom APT loaders.

---

## 🛠️ What It Does
- Creates a file with malicious payload
- Marks it for deletion before closing the file handle
- Maps it into memory **while it's already deleted**
- Executes the payload — no disk artifact remains

---

## 💣 Simplified Flow
1. Create a temporary file
2. Write malicious PE (EXE) into it
3. Mark the file `DELETE_PENDING` using `NtSetInformationFile`
4. Use `NtCreateSection` to map it into memory
5. Call `NtCreateProcessEx` or `CreateProcess` on that memory section
✅ **No file ever exists on disk in a scannable form**

---

### C++ Implementation Concept
```cpp
HANDLE hFile = CreateFile(...);
WriteFile(hFile, PEBuffer, PEsize, ...);
NtSetInformationFile(hFile, FileDispositionInfo, ...); // mark for delete
NtCreateSection(&hSection, ..., hFile);
NtCreateProcessEx(..., hSection, ...);
```
✅ Only in-memory — even EDRs that scan on-disk files miss this completely

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Processes launched without parent EXE on disk | Monitor for orphaned processes or memory-only parents |
| High entropy memory regions with RWX perms | Look for memory-mapped files without associated disk handles |
| Use of raw Windows syscalls (`NtCreateSection`, `NtCreateProcessEx`) | Monitor syscall patterns — rare in normal apps

---

## 🔐 Mitigation Strategies
- Use EDRs with memory scanning (e.g., CrowdStrike, SentinelOne with memory inspection enabled)
- Monitor for `DELETE_PENDING` file handles being mapped into memory
- Restrict low-level file APIs to signed/verified apps only
- Detect unsigned processes running from memory-only locations

---

> 🧠 This is one of the **stealthiest execution methods ever created**. No PE on disk. No easy scan. Just a ghost in the machine — and your antivirus won’t even blink.

Welcome to apex-level fileless malware ops.
