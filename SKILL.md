---
name: native-fault-analyzer
description: Catches SIGSEGV, SIGABRT, memory panics, and unhandled native process faults, parsing stack frames and heap dumps for auto-fixing.
version: 1.0.0
---

# Native Process Fault & Memory Analyzer

## Trigger Conditions
Invoke when process terminates abnormally via system signals (`SIGSEGV`, `SIGBUS`, `SIGABRT`), heap out-of-memory errors, or unhandled runtime exceptions.

## Instructions

1. **Signal Trap & Core Analysis**
   - Intercept standard process exit signals and capture `stderr` trace buffers.
   - Locate register state (`RIP`, `RSP`, `EAX`) and native signal addresses if core dumps are enabled.

2. **Symbol Mapping & Frame Unwinding**
   - Map memory address offsets back to source symbols using debug symbols (`dSYM`, `PDB`, or dwarf debug sections).
   - Differentiate between native library crashes (`/lib/x86_64-linux-gnu/`) and userland code panic points.

3. **Heap & Memory Drift Inspection**
   - Inspect allocation deltas prior to termination.
   - Detect memory leaks: identify runaway arrays, dangling pointers, circular buffer retention, or buffer overflows.

4. **Remediation Action Plan**
   - Formulate targeted fix strategy:
     - If `SIGSEGV` / Null Pointer: Identify unchecked null value or uninitialized reference at frame 0.
     - If OOM: Pinpoint allocation loop or missing stream backpressure in source file.
     - If missing dependency/ABI issue: Flag exact missing shared object (`.so`) library or symbol export.
