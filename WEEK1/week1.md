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

## 📦 Task 1: Toolchain Installation & Verification

**Status:** ![Completed](https://img.shields.io/badge/Status-Completed-green)

<details>
<summary>🎯 Expand to View Task Details</summary>

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

Update the PATH Environment Variable:
bash

Collapse

Wrap

Run

Copy
echo 'export PATH=$HOME/riscv/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
Explanation: Adds the toolchain binaries to the system PATH, ensuring tools like riscv32-unknown-elf-gcc are accessible from the terminal.
Verify the Installation:
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-gcc --version
riscv32-unknown-elf-gdb --version
riscv32-unknown-elf-objdump --version
Explanation: Confirms that the gcc compiler, gdb debugger, and objdump disassembler are installed. The prefix riscv32-unknown-elf indicates the target (32-bit RISC-V, ELF format for bare-metal).
⚠️ Issues Faced
glibc Mismatch: The toolchain required a compatible glibc version, resolved by switching to Ubuntu 24.04.
Missing libpython: Some tools depend on Python libraries, fixed by installing via the deadsnakes PPA.
gdb Not Found: The PATH was initially incorrect; fixed by sourcing .bashrc after updating.
📸 Screenshots
Toolchain version output:
PATH setup confirmation:
glibc issue debug log:
✅ Status
Completed: Toolchain successfully installed and verified.
Issues Resolved: All issues (glibc mismatch, libpython, PATH) were fixed, and the setup is fully functional.
👋 Task 2: Compiling a RISC-V C Program
Status: 

🎯 Expand to View Task Details
Objective
Write, compile, and verify a simple "Hello, RISC-V!" C program to test the toolchain.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. The base integer instructions (I) handle basic operations, while compressed instructions (C) optimize for smaller code size.
Platform: Ubuntu 24.04 LTS, providing the environment for compilation and verification.
📄 Code
c

Collapse

Wrap

Run

Copy
#include <stdio.h>
int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
Explanation: This program tests the toolchain by printing a message. Since we’re targeting a bare-metal environment, stdio.h functions like printf may not output directly without UART setup (addressed in later tasks). The focus here is on successful compilation.

🔧 Compile
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-gcc -o hello.elf hello.c
Explanation: Compiles the C code into an ELF file (hello.elf), a common format for RISC-V bare-metal programs.

🔍 Verify ELF
bash

Collapse

Wrap

Run

Copy
file hello.elf
Explanation: The file command checks the type of the compiled binary, confirming it’s a 32-bit RISC-V ELF executable.

⚠️ Issues Faced
No Output: Expected since there’s no UART setup yet; the program compiles but doesn’t display output.
📸 Screenshots
Compilation output:
ELF verification:
✅ Status
Completed: Program successfully compiled and verified as a 32-bit RISC-V ELF.
🔍 Task 3: C to Assembly Conversion
Status: 

🎯 Expand to View Task Details
Objective
Convert the C code to RISC-V assembly to understand how the compiler translates high-level code.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. The integer instructions (I) are used for basic operations, and the assembly output reflects this.
Platform: Ubuntu 24.04 LTS, used for running the compiler and generating assembly.
📄 Code
c

Collapse

Wrap

Run

Copy
#include <stdio.h>
int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
⚙️ Generate Assembly
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-gcc -S -O0 hello.c -o hello.s
Explanation: The -S flag generates assembly code (.s file). -O0 disables optimizations for readable output.

🧠 Key Assembly Snippet
asm

Collapse

Wrap

Copy
addi sp,sp,-16   # Allocate stack space
sw   ra,12(sp)   # Save return address
Explanation:

addi sp,sp,-16: Allocates 16 bytes on the stack for the function’s frame.
sw ra,12(sp): Saves the return address (ra) for function return.
⚠️ Issues Faced
Verbose Output: Assembly was longer than expected due to -O0, but this was intentional for clarity.
📸 Screenshots
Assembly file snippet:
Full assembly file:
✅ Status
Completed: Successfully generated and analyzed RISC-V assembly from C code.
🔬 Task 4: Disassembly & Hex Dump
Status: 

🎯 Expand to View Task Details
Objective
Disassemble the ELF and generate binary/hex outputs to inspect the machine code.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. The disassembled instructions are from the base integer set (I).
Platform: Ubuntu 24.04 LTS, used for running objdump and hexdump.
📄 Code
c

Collapse

Wrap

Run

Copy
#include <stdio.h>
int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
🔧 Commands
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-objcopy -O binary hello.elf hello.bin
riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
riscv32-unknown-elf-objdump -d hello.elf
hexdump -C hello.bin
Explanation:

objcopy -O binary: Converts ELF to raw binary.
objcopy -O ihex: Converts ELF to Intel HEX format.
objdump -d: Disassembles the ELF into RISC-V instructions.
hexdump -C: Displays the binary in hexadecimal.
🧠 Disassembly Breakdown
Address: Memory location (e.g., 0x00010100).
Opcode: Machine code (e.g., 0x01312623).
Mnemonic: Instruction (e.g., sw).
Operands: Arguments (e.g., ra,12(sp)).
⚠️ Issues Faced
Large Output: Disassembly was lengthy due to library code inclusion, but focused on main for analysis.
📸 Screenshots
Disassembly output:
Hex dump:
✅ Status
Completed: Successfully disassembled the ELF and generated binary/hex outputs.
📚 Task 5: ABI & Register Cheat-Sheet
Status: 

🎯 Expand to View Task Details
Objective
List all 32 RV32 integer registers with their ABI names and calling-convention roles.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. The ABI defines how registers are used in RV32.
Platform: Ubuntu 24.04 LTS, used for referencing the ABI during GDB debugging.
🔸 Register Table: RV32I Registers & ABI Roles

Register	ABI Name	Role in Calling Convention	Description
x0	zero	Constant zero	Always zero, writes ignored
x1	ra	Return address	Set by jal for function returns
x2	sp	Stack pointer	Points to top of stack
x3	gp	Global pointer	Points to global data region
x4	tp	Thread pointer	Points to thread-local storage
x5	t0	Temporary (caller-saved)	Not preserved across calls
x6	t1	Temporary (caller-saved)	Not preserved across calls
x7	t2	Temporary (caller-saved)	Not preserved across calls
x8	s0/fp	Saved register / Frame pointer	Callee-saved, often used as FP
x9	s1	Saved register (callee-saved)	Preserved across calls
x10	a0	Function argument / return value [0]	Used to pass args/return values
x11	a1	Function argument / return value [1]	Used to pass args/return values
x12	a2	Function argument [2]	Up to 8 args passed via a0–a7
x13	a3	Function argument [3]	
x14	a4	Function argument [4]	
x15	a5	Function argument [5]	
x16	a6	Function argument [6]	
x17	a7	Function argument [7]	
x18	s2	Saved register (callee-saved)	Preserved across calls
x19	s3	Saved register (callee-saved)	Preserved across calls
x20	s4	Saved register (callee-saved)	Preserved across calls
x21	s5	Saved register (callee-saved)	Preserved across calls
x22	s6	Saved register (callee-saved)	Preserved across calls
x23	s7	Saved register (callee-saved)	Preserved across calls
x24	s8	Saved register (callee-saved)	Preserved across calls
x25	s9	Saved register (callee-saved)	Preserved across calls
x26	s10	Saved register (callee-saved)	Preserved across calls
x27	s11	Saved register (callee-saved)	Preserved across calls
x28	t3	Temporary (caller-saved)	Not preserved across calls
x29	t4	Temporary (caller-saved)	Not preserved across calls
x30	t5	Temporary (caller-saved)	Not preserved across calls
x31	t6	Temporary (caller-saved)	Not preserved across calls
🔹 Calling Convention Summary

Register Type	Names	Saved By	Used For
Argument/Return	a0–a7	Caller	Function arguments, return values
Temporaries	t0–t6	Caller	Scratch data, not preserved
Saved (Callee)	s0–s11	Callee	Must be preserved across calls
Special	sp, ra, gp, tp	N/A	Stack, return address, global/thread ptrs
Constant	zero	N/A	Always reads 0, writes ignored
⚠️ Issues Faced
Understanding ABI: Took time to map registers to roles, but clarified through documentation.
📸 Screenshots
Register table reference:
GDB register values:
✅ Status
Completed: ABI and register roles documented with a comprehensive cheat-sheet.
🧪 Task 6: Debugging with GDB
Status: 

🎯 Expand to View Task Details
Objective
Debug the program using GDB with QEMU to step through instructions and inspect registers.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. Debugging uses base integer instructions (I) and the ABI for register inspection.
Platform: Ubuntu 24.04 LTS, hosting QEMU and GDB for debugging.
📄 Code
c

Collapse

Wrap

Run

Copy
#include <stdio.h>
int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
💻 Terminal 1: QEMU
bash

Collapse

Wrap

Run

Copy
qemu-riscv32 -g 1234 hello.elf
Explanation: Runs the ELF in QEMU with a GDB server on port 1234.

🧠 Terminal 2: GDB
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-gdb hello.elf
(gdb) target remote :1234
(gdb) break main
(gdb) continue
(gdb) stepi
(gdb) info registers a0
(gdb) disassemble
Explanation:

target remote :1234: Connects GDB to QEMU.
break main: Sets a breakpoint at main.
continue: Runs until the breakpoint.
stepi: Steps one instruction.
info registers a0: Displays a0’s value.
disassemble: Shows assembly around the current instruction.
💬 Observation
QEMU doesn’t print to UART, but a0 holds the address of "Hello, RISC-V!", confirming printf executed.

⚠️ Issues Faced
No UART Output: Expected, as UART isn’t set up yet; focused on register inspection instead.
📸 Screenshots
GDB disassembly:
Register a0 contents:
✅ Status
Completed: Successfully debugged the program using GDB and QEMU, inspecting registers and instructions.
🛰️ Task 7: Bare-Metal Execution with QEMU
Status: 

🎯 Expand to View Task Details
🧠 Task 8: GCC Optimization Analysis
Status: 

🎯 Expand to View Task Details
Objective
Compare assembly output with -O0 vs. -O2 to see how optimization affects code generation.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. Optimizations affect how integer instructions (I) are generated.
Platform: Ubuntu 24.04 LTS, running gcc for assembly generation.
📄 Code
c

Collapse

Wrap

Run

Copy
#include <stdio.h>
int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
🔧 Commands
bash

Collapse

Wrap

Run

Copy
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

-O0: No optimization, verbose assembly.
-O2: Aggressive optimization, smaller code.
ls -la, wc -l: Compare file sizes and line counts.
grep -A 20 "main:": Extracts main’s assembly.
diff -y: Shows differences side by side.
📊 Observations
-O2 produces smaller assembly (fewer lines).
Redundant stack operations removed.
printf setup optimized.
⚠️ Issues Faced
Complex Diff Output: -O2 removed many instructions, making diff hard to read; focused on main section.
📸 Screenshots
-O0 assembly:
-O2 assembly:
Diff output:
✅ Status
Completed: Successfully compared -O0 and -O2 assembly outputs, highlighting optimization effects.
⏱️ Task 9: Inline Assembly Basics
Status: 

🎯 Expand to View Task Details
Objective
Use inline assembly to read the RISC-V cycle counter and measure execution time of a simple operation.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. Uses the rdcycle instruction from the base set (I).
Platform: Ubuntu 24.04 LTS, running QEMU for bare-metal execution.
📄 Code
c

Collapse

Wrap

Run

Copy
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
📄 Linker Script
ld

Collapse

Wrap

Copy
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
        *(.text.*)
    } > FLASH
    .rodata : ALIGN(4) {
        *(.rodata)
        *(.rodata.*)
    } > FLASH
    .data : ALIGN(4) {
        *(.data)
        *(.data.*)
    } > RAM AT > FLASH
    .bss : ALIGN(4) {
        *(.bss)
        *(.bss.*)
    } > RAM
    _end = .;
}
📄 Startup Code
asm

Collapse

Wrap

Copy
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
🔧 Commands
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-gcc -g -O0 -march=rv32im -mabi=ilp32 -nostdlib -T linkertask9.ld -o task9.elf task9.c startuptask_9.s
qemu-system-riscv32 -nographic -machine virt -bios none -kernel task9.elf
💬 Output
text

Collapse

Wrap

Copy
Value of x: 43
Cycles taken: 8410761
⚠️ Issues Faced
High Cycle Count: Due to UART overhead; expected for I/O operations.
📸 Screenshots
QEMU output:
Code execution log:
✅ Status
Completed: Successfully measured execution time using inline assembly and rdcycle.
🔧 Task 10: Memory-Mapped I/O Demo
Status: 

🎯 Expand to View Task Details
Objective
Demonstrate memory-mapped I/O by toggling a GPIO register and prevent compiler optimizations.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. Uses base integer instructions (I) for memory-mapped I/O.
Platform: Ubuntu 24.04 LTS, running QEMU for bare-metal simulation.
📄 Code
c

Collapse

Wrap

Run

Copy
#define GPIO_OUT 0x10012000
#define UART_TX 0x10000000
#define UART_READY 0x10000005

typedef unsigned int uint32_t;

void uart_putc(char c) {
    volatile char* uart_tx = (volatile char*)UART_TX;
    volatile char* uart_ready = (volatile char*)UART_READY;
    while (!(*uart_ready & (1 << 5)));
    *uart_tx = c;
}

void uart_puts(const char* s) {
    while (*s) uart_putc(*s++);
}

void gpio_toggle(void) {
    volatile uint32_t* gpio_out = (volatile uint32_t*)GPIO_OUT;
    *gpio_out |= (1 << 0);
    *gpio_out &= ~(1 << 0);
}

int main() {
    uart_puts("GPIO Toggled\n");
    gpio_toggle();
    uart_putc('B');
    while (1) {
        uart_putc('.');
        for (volatile int i = 0; i < 100000; i++);
    }
    return 0;
}
📄 Linker Script
ld

Collapse

Wrap

Copy
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
        *(.text.*)
    } > FLASH
    .rodata : ALIGN(4) {
        *(.rodata)
        *(.rodata.*)
    } > FLASH
    .data : ALIGN(4) {
        *(.data)
        *(.data.*)
    } > RAM AT > FLASH
    .bss : ALIGN(4) {
        *(.bss)
        *(.bss.*)
    } > RAM
    _end = .;
}
📄 Startup Code
asm

Collapse

Wrap

Copy
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
🔧 Commands
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-gcc -g -O2 -march=rv32im -mabi=ilp32 -nostdlib -T linker10.ld -o task10.elf task10.c startup10.s
readelf -h task10.elf
qemu-system-riscv32 -nographic -machine virt -bios none -kernel task10.elf
💬 Output
text

Collapse

Wrap

Copy
GPIO Toggled
B.........
⚠️ Issues Faced
GPIO Not Visible: QEMU virt doesn’t emulate GPIO; verified code correctness via UART output.
📸 Screenshots
ELF header:
QEMU output:
✅ Status
Completed: Demonstrated memory-mapped I/O with GPIO toggling, verified via UART output.
📜 Task 11: Linker Script 101
Status: 

🎯 Expand to View Task Details
Objective
Implement a minimal linker script for RV32IMC to place .text at 0x00000000 and .data at 0x10000000.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. Linker script controls memory layout for RV32.
Platform: Ubuntu 24.04 LTS, used for compilation and linking.
📄 Code
c

Collapse

Wrap

Copy
#include <stdint.h>
uint32_t global_var = 0x12345678;
uint32_t bss_var;
void test_function(void) {
    global_var = 0xABCDEF00;
    bss_var = 0x11111111;
}
void main(void) {
    test_function();
    while(1);
}
📄 Startup Code
asm

Collapse

Wrap

Copy
.section .text.start
.global _start
_start:
    lui sp, %hi(_stack_top)
    addi sp, sp, %lo(_stack_top)
    call main
1:  j 1b
.size _start, . - _start
📄 Linker Script
ld

Collapse

Wrap

Copy
ENTRY(_start)
MEMORY
{
    FLASH (rx) : ORIGIN = 0x00000000, LENGTH = 256K
    SRAM  (rwx): ORIGIN = 0x10000000, LENGTH = 64K
}
SECTIONS
{
    .text 0x00000000 : {
        *(.text.start)
        *(.text*)
        *(.rodata*)
    } > FLASH
    .data 0x10000000 : {
        _data_start = .;
        *(.data*)
        _data_end = .;
    } > SRAM
    .bss : {
        _bss_start = .;
        *(.bss*)
        *(COMMON)
        _bss_end = .;
    } > SRAM
    _stack_top = ORIGIN(SRAM) + LENGTH(SRAM);
}
📄 Build Script
bash

Collapse

Wrap

Run

Copy
#!/bin/bash
echo "=== Task 11: Linker Script Implementation ==="
echo "1. Compiling assembly and C files..."
riscv32-unknown-elf-gcc -c startup11.s -o startup11.o
riscv32-unknown-elf-gcc -c task11.c -o task11.o
echo "2. Linking with custom linker script..."
riscv32-unknown-elf-ld -T linker11.ld startup11.o task11.o -o task11.elf
echo "✓ Compilation and linking successful!"
echo -e "\n3. Verifying memory layout (section addresses):"
riscv32-unknown-elf-objdump -h task11.elf | grep -E "(.text|.data|.bss)"
echo -e "\n4. Symbol addresses (first 10):"
riscv32-unknown-elf-nm task11.elf | head -10
echo -e "\n✓ Linker script working correctly!"
🔧 Commands
bash

Collapse

Wrap

Run

Copy
chmod +x build_linker_test.sh
./build_linker_test.sh
💬 Output
Section Addresses
text

Collapse

Wrap

Copy
0 .text         00001000  00000000  00000000  00001000  2**1
  1 .sdata        00002000  10000000  10000000  00002000  2**2
  2 .sbss         00002004  10000004  10000004  00002004  2**2
Symbol Addresses
text

Collapse

Wrap

Copy
10002004 D _bss_end
10000004 D _bss_start
10000004 B bss_var
10000004 D _data_end
10000000 D _data_start
10000000 D global_var
0000003e T main
10010000 D _stack_top
00000000 T _start
0000000c T test_function
⚠️ Issues Faced
Alignment Errors: Initial linker script had misaligned sections; fixed with proper alignment.
📸 Screenshots
Section addresses:
Symbol addresses:
✅ Status
Completed: Linker script successfully implemented and verified.
🚀 Task 12: Start-up Code & crt0
Status: 

🎯 Expand to View Task Details
Objective
Explain what crt0.S does in a bare-metal RISC-V program and where to get one.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. Start-up code uses base integer instructions (I).
Platform: Ubuntu 24.04 LTS, used for referencing start-up code examples.
🔸 Key Responsibilities of crt0.S

Step	Action	Description
1	Stack pointer initialization	Set sp to top of RAM for stack usage
2	Zero .bss section	Clear uninitialized variables to zero
3	Copy .data section	Copy initialized data from ROM to RAM
4	Call main()	Transfer control to C main() function
5	Infinite loop after main()	Trap if main() returns (optional: halt CPU)
📄 Minimal Example: crt0.S
asm

Collapse

Wrap

Copy
.section .text
.globl _start
_start:
    la sp, _stack_top
    la a0, __bss_start
    la a1, __bss_end
zero_bss:
    beq a0, a1, bss_done
    sw zero, 0(a0)
    addi a0, a0, 4
    j zero_bss
bss_done:
    call main
hang:
    j hang
🔹 Summary: crt0.S Essentials

Component	Purpose
sp setup	Prepares environment for stack-based calls
.bss zeroing	Ensures uninitialized variables start as zero
.data copy	Ensures initialized data is accessible in RAM
main() call	Hands over control to user-defined code
Infinite loop	Prevents undefined behavior if main() returns
🔸 Where to Find crt0.S?

Source	Use Case
Newlib	Common in embedded systems
Platform SDKs	SiFive, Kendryte, Espressif SDKs
Bare-metal Examples	GitHub: riscv-blinky, riscv-bare-metal-template
Custom Development	Adaptable for specific SoC/memory layout
⚠️ Issues Faced
Finding Examples: Took time to locate relevant crt0.S examples; resolved via GitHub search.
📸 Screenshots
crt0.S example:
Reference documentation:
✅ Status
Completed: Explained crt0.S responsibilities and sources.
⏰ Task 13: Interrupt Primer
Status: 

🎯 Expand to View Task Details
Objective
Enable the machine-timer interrupt (MTIP) and write a simple handler in C/asm.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMC. Uses machine-mode interrupts and timer instructions.
Platform: Ubuntu 24.04 LTS, running QEMU for bare-metal simulation.
📄 Code: Timer Interrupt Demo
c

Collapse

Wrap

Run

Copy
#define UART_TX 0x10000000
#define UART_READY 0x10000005
#define MTIME 0x0200bff8
#define MTIMECMP 0x02004000

typedef unsigned int uint32_t;
typedef unsigned long long uint64_t;

void uart_putc(char c) {
    volatile char* uart_tx = (volatile char*)UART_TX;
    volatile char* uart_ready = (volatile char*)UART_READY;
    while (!(*uart_ready & (1 << 5)));
    *uart_tx = c;
}

void uart_puts(const char* s) {
    while (*s) uart_putc(*s++);
}

void timer_handler(void) {
    uart_puts("MTIP\n");
    volatile uint64_t* mtime = (volatile uint64_t*)MTIME;
    volatile uint64_t* mtimecmp = (volatile uint64_t*)MTIMECMP;
    *mtimecmp = *mtime + 1000000;
}

void enable_timer_interrupt(void) {
    volatile uint64_t* mtime = (volatile uint64_t*)MTIME;
    volatile uint64_t* mtimecmp = (volatile uint64_t*)MTIMECMP;
    *mtimecmp = *mtime + 1000000;
    asm volatile ("li t0, 0x80");
    asm volatile ("csrs mie, t0");
    asm volatile ("csrs mstatus, 0x8");
}

int main() {
    uart_putc('A');
    enable_timer_interrupt();
    uart_puts("Timer enabled\n");
    while (1) {
        uart_putc('.');
        for (volatile int i = 0; i < 100000; i++);
    }
    return 0;
}
📄 Trap Handler
asm

Collapse

Wrap

Copy
.section .text
.global trap_handler
.align 4
trap_handler:
    addi sp, sp, -64
    sw ra, 0(sp)
    sw t0, 4(sp)
    sw t1, 8(sp)
    csrr t0, mcause
    li t1, 0x80000007
    bne t0, t1, skip
    jal timer_handler
skip:
    lw ra, 0(sp)
    lw t0, 4(sp)
    lw t1, 8(sp)
    addi sp, sp, 64
    mret
📄 Startup Code
asm

Collapse

Wrap

Copy
.section .text.start
.global _start
_start:
    la sp, _stack_top
    li t0, 0x10000005
    li t1, 0x20
wait_uart:
    lb t2, 0(t0)
    and t2, t2, t1
    beq t2, zero, wait_uart
    li t0, 0x10000000
    li t1, 'S'
    sb t1, 0(t0)
    la t0, trap_handler
    csrw mtvec, t0
    jal main
    j .
.section .bss
.align 4
.space 1024
_stack_top:
📄 Linker Script
ld

Collapse

Wrap

Copy
OUTPUT_ARCH(riscv)
ENTRY(_start)
MEMORY
{
    FLASH (rx) : ORIGIN = 0x80000000, LENGTH = 16M
    RAM   (rw) : ORIGIN = 0x81000000, LENGTH = 16M
}
SECTIONS
{
    .text : {
        *(.text.start)
        *(.text)
        *(.text.*)
    } > FLASH
    .rodata : ALIGN(4) {
        *(.rodata)
        *(.rodata.*)
    } > FLASH
    .data : ALIGN(4) {
        *(.data)
        *(.data.*)
    } > RAM AT > FLASH
    .bss : ALIGN(4) {
        *(.bss)
        *(.bss.*)
    } > RAM
    _end = .;
}
🔧 Commands
bash

Collapse

Wrap

Run

Copy
riscv32-unknown-elf-gcc -g -O0 -march=rv32im -mabi=ilp32 -nostdlib -T linker13.ld -o timer.elf timer_interrupt.c startup13.s trap_handler.s
qemu-system-riscv32 -nographic -machine virt -bios none -kernel timer.elf
💬 Output
text

Collapse

Wrap

Copy
SA
Timer enabled
........MTIP
........MTIP
........MTIP
⚠️ Issues Faced
Interrupt Not Firing: mie and mstatus bits were initially set incorrectly; fixed with proper CSR writes.
📸 Screenshots
QEMU output:
Interrupt log:
✅ Status
Completed: Successfully enabled and handled MTIP with a simple handler.
🧬 Task 14: RV32IMAC vs RV32IMC – What’s the “A”?
Status: 

🎯 Expand to View Task Details
Objective
Explain the ‘A’ (atomic) extension in RV32IMAC, its instructions, and their usefulness.

🏛️ Architecture & Platform
Architecture: RISC-V RV32IMAC vs RV32IMC. Focuses on the atomic extension (A).
Platform: Ubuntu 24.04 LTS, used for researching and documenting ISA extensions.
🔸 Base Comparison: RV32IMC vs RV32IMAC

Feature	RV32IMC	RV32IMAC
I (Base Integer)	✅	✅
M (Multiply/Divide)	✅	✅
C (Compressed Instr.)	✅	✅
A (Atomic)	❌	✅
🔸 What is the "A" Extension?
The 'A' extension introduces atomic read-modify-write operations for safe concurrent code in multi-core systems.

🔸 Key Atomic Instructions

Instruction	Meaning	Use Case
lr.w	Load-Reserved	Starts atomic operation
sc.w	Store-Conditional	Stores if reservation valid
amoadd.w	Atomic ADD	Adds value atomically
amoswap.w	Atomic SWAP	Swaps memory and register values
amoor.w	Atomic OR	Sets flags atomically
amoand.w	Atomic AND	Locks bits
amomin.w	Atomic Min	Priority/resource arbitration
amomax.w	Atomic Max	Max-tracking use cases
🔹 Importance of ‘A’

Purpose	Why It’s Needed
Thread-safe Operations	Allows safe memory sharing
Lock-Free Programming	Enables deadlock-free data structures
OS Support	Implements mutexes, semaphores, spinlocks
Multicore Support	Synchronizes memory across cores
🔸 Visual Summary
text

Collapse

Wrap

Copy
RV32IMC                           RV32IMAC
--------------------------------   -----------------------------------
 Base + Mul/Div + Compressed       Base + Mul/Div + Compressed + ⚡Atomic

             ❌                             ✅ Supports:
                                          - lr.w, sc.w
                                          - amoadd.w, amoswap.w
                                          - Enables thread-safe systems
⚠️ Issues Faced
Understanding Use Cases: Took time to research atomic instruction applications; clarified via RISC-V specs.
📸 Screenshots
Comparison table:
Instruction list:
✅ Status
Completed: Explained the ‘A’ extension, its instructions, and their importance.
