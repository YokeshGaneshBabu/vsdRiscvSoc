# 🚀 VSD SoC Labs — Week 1: RISC-V Toolchain & Debugging

🌟 **Goal:** Set up a RISC-V environment, compile and analyze C programs, explore the ABI, debug with GDB, boot bare-metal ELF on QEMU, use inline assembly, perform memory-mapped I/O, implement a custom linker script, understand start-up code, handle interrupts, and compare RV32IMAC vs RV32IMC.

🗓️ **Date & Time:** 06:24 PM IST, Sunday, June 08, 2025  
💻 **Platform:** Ubuntu 24.04 LTS  
📊 **Progress:** Completed
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

**Status:** Completed

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
- **glibc Mismatch:** The toolchain required a compatible glibc version, resolved by switching to Ubuntu 24.04.
- **Missing libpython:** Some tools depend on Python libraries, fixed by installing via the deadsnakes PPA.
- **gdb Not Found:** The PATH was initially incorrect; fixed by sourcing .bashrc after updating.

✅ **Status**
- **Completed:** Toolchain successfully installed and verified.
- **Issues Resolved:** All issues (glibc mismatch, libpython, PATH) were fixed, and the setup is fully functional.

## 👋 Task 2: Compiling a RISC-V C Program

**Status:** Completed

### Objective
Write, compile, and verify a simple "Hello, RISC-V!" C program to test the toolchain.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. The base integer instructions (I) handle basic operations, while compressed instructions (C) optimize for smaller code size.
- **Platform**: Ubuntu 24.04 LTS, providing the environment for compilation and verification.

### 📄 Code

      #include <stdio.h>
      int main() {
       printf("Hello, RISC-V!\n");
      return 0;
      }
Explanation: This program tests the toolchain by printing a message. Since we’re targeting a bare-metal environment, stdio.h functions like printf may not output directly without UART setup (addressed in later tasks). The focus here is on successful compilation.

   ### 🔧 Compile
    riscv32-unknown-elf-gcc -o hello.elf hello.c
Explanation: Compiles the C code into an ELF file (hello.elf), a common format for RISC-V bare-metal programs.

   ### 🔍 Verify ELF
    file hello.elf
Explanation: The file command checks the type of the compiled binary, confirming it’s a 32-bit RISC-V ELF executable.

✅ **Status**
Completed: Program successfully compiled and verified as a 32-bit RISC-V ELF.

## 🔍 Task 3: C to Assembly Conversion

**Status:** Completed

### Objective
Convert the C code to RISC-V assembly to understand how the compiler translates high-level code.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. The integer instructions (I) are used for basic operations, and the assembly output reflects this.
- **Platform**: Ubuntu 24.04 LTS, used for running the compiler and generating assembly.

### 📄 Code
    #include <stdio.h>
    int main() {
    printf("Hello, RISC-V!\n");
    return 0;
    }
Explanation: The same code from Task 2 is used to generate assembly.

### ⚙️ Generate Assembly
    riscv32-unknown-elf-gcc -S -O0 hello.c -o hello.s
Explanation: The -S flag generates assembly code (.s file). -O0 disables optimizations for readable output.

### 🧠 Key Assembly Snippet
    addi sp,sp,-16   # Allocate stack space
    sw   ra,12(sp)   # Save return address
Explanation:  
- addi sp,sp,-16: Allocates 16 bytes on the stack for the function’s frame.  
- sw ra,12(sp): Saves the return address (ra) for function return.

⚠️ **Issues Faced**
- Verbose Output: Assembly was longer than expected due to -O0, but this was intentional for clarity.

✅ **Status**
Completed: Successfully generated and analyzed RISC-V assembly from C code.

## 🔬 Task 4: Disassembly & Hex Dump

**Status:** Completed

### Objective
Disassemble the ELF and generate binary/hex outputs to inspect the machine code.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. The disassembled instructions are from the base integer set (I).
- **Platform**: Ubuntu 24.04 LTS, used for running objdump and hexdump.

### 📄 Code
    #include <stdio.h>
    int main() {
    printf("Hello, RISC-V!\n");
    return 0;
    }
Explanation: The same code from Task 2 is used for disassembly.

### 🔧 Commands
    riscv32-unknown-elf-objcopy -O binary hello.elf hello.bin
    riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
    riscv32-unknown-elf-objdump -d hello.elf
    hexdump -C hello.bin
Explanation:  
- objcopy -O binary: Converts ELF to raw binary.  
- objcopy -O ihex: Converts ELF to Intel HEX format.  
- objdump -d: Disassembles the ELF into RISC-V instructions.  
- hexdump -C: Displays the binary in hexadecimal.

### 🧠 Disassembly Breakdown
- **Address**: Memory location (e.g., 0x00010100).  
- **Opcode**: Machine code (e.g., 0x01312623).  
- **Mnemonic**: Instruction (e.g., sw).  
- **Operands**: Arguments (e.g., ra,12(sp)).

⚠️ **Issues Faced**
- Large Output: Disassembly was lengthy due to library code inclusion, but focused on main for analysis.

✅ **Status**
Completed: Successfully disassembled the ELF and generated binary/hex outputs.

## 📚 Task 5: ABI & Register Cheat-Sheet

**Status:** Completed

### Objective
List all 32 RV32 integer registers with their ABI names and calling-convention roles.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. The ABI defines how registers are used in RV32.
- **Platform**: Ubuntu 24.04 LTS, used for referencing the ABI during GDB debugging.

### 🔸 Register Table: RV32I Registers & ABI Roles
| Register | ABI Name | Role in Calling Convention         | Description                          |
|----------|----------|------------------------------------|--------------------------------------|
| x0       | zero     | Constant zero                     | Always zero, writes ignored          |
| x1       | ra       | Return address                    | Set by jal for function returns      |
| x2       | sp       | Stack pointer                     | Points to top of stack               |
| x3       | gp       | Global pointer                    | Points to global data region         |
| x4       | tp       | Thread pointer                    | Points to thread-local storage       |
| x5       | t0       | Temporary (caller-saved)          | Not preserved across calls           |
| x6       | t1       | Temporary (caller-saved)          | Not preserved across calls           |
| x7       | t2       | Temporary (caller-saved)          | Not preserved across calls           |
| x8       | s0/fp    | Saved register / Frame pointer    | Callee-saved, often used as FP       |
| x9       | s1       | Saved register (callee-saved)     | Preserved across calls               |
| x10      | a0       | Function argument / return value [0] | Used to pass args/return values   |
| x11      | a1       | Function argument / return value [1] | Used to pass args/return values   |
| x12      | a2       | Function argument [2]             | Up to 8 args passed via a0–a7        |
| x13      | a3       | Function argument [3]             |                                      |
| x14      | a4       | Function argument [4]             |                                      |
| x15      | a5       | Function argument [5]             |                                      |
| x16      | a6       | Function argument [6]             |                                      |
| x17      | a7       | Function argument [7]             |                                      |
| x18      | s2       | Saved register (callee-saved)     | Preserved across calls               |
| x19      | s3       | Saved register (callee-saved)     | Preserved across calls               |
| x20      | s4       | Saved register (callee-saved)     | Preserved across calls               |
| x21      | s5       | Saved register (callee-saved)     | Preserved across calls               |
| x22      | s6       | Saved register (callee-saved)     | Preserved across calls               |
| x23      | s7       | Saved register (callee-saved)     | Preserved across calls               |
| x24      | s8       | Saved register (callee-saved)     | Preserved across calls               |
| x25      | s9       | Saved register (callee-saved)     | Preserved across calls               |
| x26      | s10      | Saved register (callee-saved)     | Preserved across calls               |
| x27      | s11      | Saved register (callee-saved)     | Preserved across calls               |
| x28      | t3       | Temporary (caller-saved)          | Not preserved across calls           |
| x29      | t4       | Temporary (caller-saved)          | Not preserved across calls           |
| x30      | t5       | Temporary (caller-saved)          | Not preserved across calls           |
| x31      | t6       | Temporary (caller-saved)          | Not preserved across calls           |

### 🔹 Calling Convention Summary
| Register Type   | Names    | Saved By | Used For                          |
|-----------------|----------|----------|-----------------------------------|
| Argument/Return | a0–a7    | Caller   | Function arguments, return values |
| Temporaries     | t0–t6    | Caller   | Scratch data, not preserved       |
| Saved (Callee)  | s0–s11   | Callee   | Must be preserved across calls    |
| Special         | sp, ra, gp, tp | N/A    | Stack, return address, global/thread ptrs |
| Constant        | zero     | N/A      | Always reads 0, writes ignored    |

⚠️ **Issues Faced**
- Understanding ABI: Took time to map registers to roles, but clarified through documentation.

✅ **Status**
Completed: ABI and register roles documented with a comprehensive cheat-sheet.

## 🧪 Task 6: Debugging with GDB

**Status:** Completed

### Objective
Debug the program using GDB with QEMU to step through instructions and inspect registers.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Debugging uses base integer instructions (I) and the ABI for register inspection.
- **Platform**: Ubuntu 24.04 LTS, hosting QEMU and GDB for debugging.

### 📄 Code
    #include <stdio.h>
    int main() {
       printf("Hello, RISC-V!\n");
       return 0;
    }
Explanation: The same code from Task 2 is used for debugging.

### 💻 Terminal 1: QEMU
    qemu-riscv32 -g 1234 hello.elf
Explanation: Runs the ELF in QEMU with a GDB server on port 1234.

### 🧠 Terminal 2: GDB
     riscv32-unknown-elf-gdb hello.elf
    (gdb) target remote :1234
    (gdb) break main
    (gdb) continue
    (gdb) stepi
    (gdb) info registers a0
    (gdb) disassemble
Explanation:  
- target remote :1234: Connects GDB to QEMU.  
- break main: Sets a breakpoint at main.  
- continue: Runs until the breakpoint.  
- stepi: Steps one instruction.  
- info registers a0: Displays a0’s value.  
- disassemble: Shows assembly around the current instruction.

### 💬 Observation
Explanation: QEMU doesn’t print to UART, but a0 holds the address of "Hello, RISC-V!", confirming printf executed.

⚠️ **Issues Faced**
- No UART Output: Expected, as UART isn’t set up yet; focused on register inspection instead.

✅ **Status**
Completed: Successfully debugged the program using GDB and QEMU, inspecting registers and instructions.

## 🛰️ Task 7: Bare-Metal Execution with QEMU

**Status:** Completed

### Objective
Run a bare-metal ELF using QEMU and OpenSBI to simulate a real RISC-V system.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Bare-metal execution uses the base integer set (I) for minimal setup.
- **Platform**: Ubuntu 24.04 LTS, running QEMU for emulation and OpenSBI for firmware.

### 📄 Code: Bare-Metal UART Program

    #define UART_TX 0x10000000 // UART transmit register for QEMU virt
    #define UART_READY 0x10000005 // UART status (bit 5 = TX ready)

    void uart_putc(char c) {
       volatile char* uart_tx = (volatile char*)UART_TX;
       volatile char* uart_ready = (volatile char*)UART_READY;
       while (!(*uart_ready & (1 << 5)));
       *uart_tx = c;
    }

    void uart_puts(const char* s) {
       while (*s) uart_putc(*s++);
    }
   
    int main() {
       uart_puts("Hello from bare-metal!\n");
       while (1);
       return 0;
    }
Explanation: A bare-metal program that uses UART to output a message.

### 📄 Startup Code

      .section .text.start
      .global _start
      _start:
       la sp, _stack_top
       jal main
       j .
      .section .bss
      .align 4
      .space 1024
      _stack_top:
Explanation: Sets up the stack pointer and jumps to main.

### 📄 Linker Script
    OUTPUT_ARCH(riscv)
    ENTRY(_start)
    MEMORY
    {
    FLASH (rx) : ORIGIN = 0x80000000, LENGTH = 16M
    RAM (rw)   : ORIGIN = 0x81000000, LENGTH = 16M
    }
    SECTIONS
    {
    .text : {
    *(.text.start)
    *(.text)
    (.text.)
    } > FLASH
    .rodata : ALIGN(4) {
    *(.rodata)
    (.rodata.)
    } > FLASH
    .data : ALIGN(4) {
    *(.data)
    (.data.)
    } > RAM AT > FLASH
    .bss : ALIGN(4) {
    *(.bss)
    (.bss.)
    } > RAM
    _end = .;
    }
Explanation: Defines memory layout for FLASH and RAM, placing sections appropriately.

### 🔧 Commands
curl -LO https://github.com/qemu/qemu/raw/v8.0.4/pc-bios/opensbi-riscv32-generic-fw_dynamic.bin
qemu-system-riscv32 -nographic -machine virt -bios opensbi-riscv32-generic-fw_dynamic.bin -kernel hello2.elf
Explanation:  
- Downloads OpenSBI firmware for RISC-V booting.  
- qemu-system-riscv32: Emulates a RISC-V system using the virt machine.  
- -nographic: Uses terminal I/O.  
- -bios: Loads OpenSBI firmware.  
- -kernel: Executes the ELF.

⚠️ **Issues Faced**
- No UART Output: UART address was incorrect; fixed by verifying QEMU virt machine documentation.
- Terminal Freezes: Misconfiguration in QEMU; resolved by adjusting flags.
- Only OpenSBI Banner: ELF wasn’t executing; fixed by correcting linker script.

✅ **Status**
Completed: Bare-metal ELF successfully executed with QEMU and OpenSBI, displaying UART output.

## 🧠 Task 8: GCC Optimization Analysis

**Status:** Completed

### Objective
Compare assembly output with -O0 vs. -O2 to see how optimization affects code generation.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Optimizations affect how integer instructions (I) are generated.
- **Platform**: Ubuntu 24.04 LTS, running gcc for assembly generation.

### 📄 Code
   
    #include <stdio.h>
    int main() {
       printf("Hello, RISC-V!\n");
       return 0;
    }
Explanation: The same code from Task 2 is used for optimization analysis.

### 🔧 Commands

    riscv32-unknown-elf-gcc -S -O0 hello.c -o helloO0.s
    riscv32-unknown-elf-gcc -S -O2 hello.c -o helloO2.s
    ls -la helloO0.s
    ls -la helloO2.s
    wc -l helloO0.s
    wc -l helloO2.s
    grep -A 20 "main:" helloO0.s
    grep -A 20 "main:" helloO2.s
    diff -y helloO0.s helloO2.s
Explanation:  
- -O0: No optimization, verbose assembly.  
- -O2: Aggressive optimization, smaller code.  
- ls -la, wc -l: Compare file sizes and line counts.  
- grep -A 20 "main:": Extracts main’s assembly.  
- diff -y: Shows differences side by side.

### 📊 Observations
Explanation:  
- -O2 produces smaller assembly (fewer lines).  
- Redundant stack operations removed.  
- printf setup optimized.

⚠️ **Issues Faced**
- Complex Diff Output: -O2 removed many instructions, making diff hard to read; focused on main section.

✅ **Status**
Completed: Successfully compared -O0 and -O2 assembly outputs, highlighting optimization effects.

## ⏱️ Task 9: Inline Assembly Basics

**Status:** Completed

### Objective
Use inline assembly to read the RISC-V cycle counter and measure execution time of a simple operation.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Uses the rdcycle instruction from the base set (I).
- **Platform**: Ubuntu 24.04 LTS, running QEMU for bare-metal execution.

### 📄 Code

    #include <stdint.h>

    #define UART_TX 0x10000000
    #define UART_READY 0x10000005
   
    void uart_putc(char c) {
       volatile char* uart_tx = (volatile char*)UART_TX;
       volatile char* uart_ready = (volatile char*)UART_READY;
       while (!(*uart_ready & (1 << 5)));
       *uart_tx = c;
    }
   
    void uart_puts(const char* s) {
       while (*s) uart_putc(*s++);
    }
   
    uint32_t read_cycle_counter(void) {
       uint32_t cycles;
       __asm__ volatile (
           "rdcycle %0"
           : "=r" (cycles)
           :
           :
       );
       return cycles;
    }
   
    void uart_put_num(uint32_t num) {
        if (num == 0) {
           uart_putc('0');
       } else {
           char buf[10];
           int i = 0;
           while (num > 0) {
               buf[i++] = '0' + (num % 10);
               num /= 10;
           }
           while (i > 0) uart_putc(buf[--i]);
       }
    }
   
    int main() {
       uint32_t start = read_cycle_counter();
       volatile int x = 42;
       x = x + 1;
       uart_puts("Value of x: ");
       uart_put_num(x);
       uart_puts("\n");
       uint32_t end = read_cycle_counter();
       uart_puts("Cycles taken: ");
       uart_put_num(end - start);
       uart_puts("\n");
       return x;
    }
Explanation: Uses inline assembly to read the cycle counter and measure a simple operation’s execution time.

### 📄 Linker Script
    OUTPUT_ARCH(riscv)
    ENTRY(_start)
    MEMORY
    {
    FLASH (rx) : ORIGIN = 0x80000000, LENGTH = 16M
    RAM (rw)   : ORIGIN = 0x81000000, LENGTH = 16M
    }
    SECTIONS
    {
    . = 0x80000000;
    .text : {
    *(.text.start)
     *(.text)
    (.text.)
    } > FLASH
    .rodata : ALIGN(4) {
    *(.rodata)
    (.rodata.)
    } > FLASH
    .data : ALIGN(4) {
    *(.data)
    (.data.)
    } > RAM AT > FLASH
    .bss : ALIGN(4) {
    *(.bss)
    (.bss.)
    } > RAM
    _end = .;
    }
Explanation: Defines memory layout for the bare-metal program.




