# 🚀 VSD SoC Labs — Week 1: RISC-V Toolchain & Debugging

🌟 **Goal:** Set up a RISC-V environment, compile and analyze C programs, explore the ABI, debug with GDB, boot bare-metal ELF on QEMU, use inline assembly, perform memory-mapped I/O, implement a custom linker script, understand start-up code, handle interrupts, and compare RV32IMAC vs RV32IMC.

🗓️ **Date & Time:** 06:24 PM IST, Sunday, June 08, 2025  
💻 **Platform:** Ubuntu 24.04 LTS  
📊 **Progress:** <image-card alt="Progress" src="https://progress-bar.dev/100/" ></image-card>

## 📋 Overview

This repository documents Week 1 tasks of the VSD SoC Labs, focusing on the RISC-V toolchain, C-to-assembly translation, disassembly, ABI understanding, GDB debugging, bare-metal execution with QEMU and OpenSBI, inline assembly for performance measurement, memory-mapped I/O for GPIO toggling, linker script implementation, start-up code analysis, interrupt handling, and ISA extension comparison. Each task includes commands, code, detailed explanations, and issues faced. All code is embedded directly in this README for simplicity.

## 📖 Table of Contents

- [Task 1: Toolchain Installation & Verification](#task-1-toolchain-installation--verification)  
- [Task 2: Compiling a RISC-V C Program](#task-2-compiling-a-risc-v-c-program)  
- [Task 3: C to Assembly Conversion](#task-3-c-to-assembly-conversion)  
- [Task 4: Disassembly & Hex Dump](#task-4-disassembly--hex-dump)  
- [Task 5: ABI & Register Cheat-Sheet](#task-5-abi--register-cheat-sheet)  
- [Task 6: Debugging with GDB](#task-6-debugging-with-gdb)  
- [Task 7: Bare-Metal Execution with QEMU](#task-7-bare-metal-execution-with-qemu)  
- [Task 8: GCC Optimization Analysis](#task-8-gcc-optimization-analysis)  
- [Task 9: Inline Assembly Basics](#task-9-inline-assembly-basics)  
- [Task 10: Memory-Mapped I/O Demo](#task-10-memory-mapped-io-demo)  
- [Task 11: Linker Script 101](#task-11-linker-script-101)  
- [Task 12: Start-up Code & crt0](#task-12-start-up-code--crt0)  
- [Task 13: Interrupt Primer](#task-13-interrupt-primer)  
- [Task 14: RV32IMAC vs RV32IMC – What’s the “A”?](#task-14-rv32imac-vs-rv32imc--whats-the-a)

## 📦 Task 1: Toolchain Installation & Verification

**Status:** <image-card alt="Completed" src="https://img.shields.io/badge/Status-Completed-green" ></image-card>

### Objective
Install and verify the RISC-V GNU toolchain to enable compiling and debugging RISC-V programs on a 32-bit RISC-V architecture.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC (32-bit base integer, multiply/divide, compressed instructions). This architecture provides a lightweight instruction set for embedded systems, with compressed instructions (C) reducing code size.
- **Platform**: Ubuntu 24.04 LTS, a stable Linux distribution, used for hosting the toolchain and running QEMU for emulation.

### 🔧 Steps
The goal is to set up the RISC-V GNU toolchain, which includes the compiler (`gcc`), debugger (`gdb`), and utilities (`objdump`, `objcopy`).

1. **Download and Extract the Prebuilt Toolchain**:
   ```bash
   wget https://vsd-labs.sgp1.cdn.digitaloceanspaces.com/vsd-labs/riscv-toolchain-rv32imac-x86_64-Ubuntu.tar.gz
   tar -xzvf riscv-toolchain-rv32imac-x86_64-Ubuntu.tar.gz
   mv riscv $HOME/riscv
Explanation: Downloads a prebuilt toolchain for RV32IMAC, extracts it, and moves it to $HOME/riscv for easy access.

2. **Update the PATH Environment Variable**:
   ```bash
   echo 'export PATH=$HOME/riscv/bin:$PATH' >> ~/.bashrc
   source ~/.bashrc
Explanation: Adds the toolchain binaries to the system PATH, ensuring tools like riscv32-unknown-elf-gcc are accessible from the terminal.

3. **Verify the Installation**:
   ```bash
   riscv32-unknown-elf-gcc --version
   riscv32-unknown-elf-gdb --version
   riscv32-unknown-elf-objdump --version
Explanation: Confirms that the gcc compiler, gdb debugger, and objdump disassembler are installed. The prefix riscv32-unknown-elf indicates the target (32-bit RISC-V, ELF format for bare-metal).

⚠️ **Issues Faced**
-glibc Mismatch: The toolchain required a compatible glibc version, resolved by switching to Ubuntu 24.04.
-Missing libpython: Some tools depend on Python libraries, fixed by installing via the deadsnakes PPA.
-gdb Not Found: The PATH was initially incorrect; fixed by sourcing .bashrc after updating.

✅ **Status**
Completed: Toolchain successfully installed and verified.
Issues Resolved: All issues (glibc mismatch, libpython, PATH) were fixed, and the setup is fully functional.


