# 🚀 VSD SoC Labs — Week 1: RISC-V Toolchain & Debugging

🌟 **Goal:** Set up a RISC-V environment, compile and analyze C programs, explore the ABI, debug with GDB, boot bare-metal ELF on QEMU, use inline assembly, perform memory-mapped I/O, implement a custom linker script, understand start-up code, handle interrupts, and compare RV32IMAC vs RV32IMC.

🗓️ **Date & Time:** 06:07 PM IST, Sunday, June 08, 2025  
💻 **Platform:** Ubuntu 24.04 LTS  
📊 **Progress:** ![Progress](https://progress-bar.dev/100/)

---

## 📋 Overview

This repository documents Week 1 tasks of the VSD SoC Labs, focusing on the RISC-V toolchain, C-to-assembly translation, disassembly, ABI understanding, GDB debugging, bare-metal execution with QEMU and OpenSBI, inline assembly for performance measurement, memory-mapped I/O for GPIO toggling, linker script implementation, start-up code analysis, interrupt handling, and ISA extension comparison. Each task includes commands, code, detailed explanations, issues faced, and screenshot galleries for visual proof. All code is embedded directly in this README for simplicity.

---

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

---

