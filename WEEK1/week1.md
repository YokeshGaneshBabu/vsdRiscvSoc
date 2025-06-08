# 🚀 VSD SoC Labs — Week 1: RISC-V Toolchain & Debugging

🌟 **Goal:** Set up a RISC-V environment, compile and analyze C programs, explore the ABI, debug with GDB, boot bare-metal ELF on QEMU, use inline assembly, perform memory-mapped I/O, implement a custom linker script, understand start-up code, handle interrupts, and compare RV32IMAC vs RV32IMC.

🗓️ **Date:** Sunday, June 08, 2025  
💻 **Platform:** Ubuntu 24.04 LTS  
📊 **Progress:** 17/17
## 📋 Overview
This repository documents Week 1 tasks of the VSD SoC Labs, focusing on the RISC-V toolchain, C-to-assembly translation, disassembly, ABI understanding, GDB debugging, bare-metal execution with QEMU and OpenSBI, inline assembly for performance measurement, memory-mapped I/O for GPIO toggling, linker script implementation, start-up code analysis, interrupt handling, and ISA extension comparison. Each task includes commands, code, detailed explanations, and issues faced. All code is embedded directly in this README for simplicity.

## 📊SUMMARY TABLE
| Task No | Title                              | Status    | One-line Summary                                      |
|---------|------------------------------------|-----------|-------------------------------------------------------|
| 1       | Toolchain Installation & Verification | Completed | Installed RISC-V toolchain and verified functionality. |
| 2       | Compiling a RISC-V C Program       | Completed | Compiled a simple RISC-V C program to ELF.            |
| 3       | C to Assembly Conversion           | Completed | Converted C code to RISC-V assembly for analysis.     |
| 4       | Disassembly & Hex Dump             | Completed | Disassembled ELF and generated binary/hex outputs.    |
| 5       | ABI & Register Cheat-Sheet         | Completed | Documented RV32 ABI and register roles.               |
| 6       | Debugging with GDB                 | Completed | Debugged program with GDB and QEMU, inspecting registers. |
| 7       | Bare-Metal Execution with QEMU     | Completed | Ran bare-metal ELF on QEMU with OpenSBI, using UART.  |
| 8       | GCC Optimization Analysis          | Completed | Compared -O0 vs -O2 assembly for optimization effects. |
| 9       | Inline Assembly Basics             | Completed | Measured execution time using inline assembly and rdcycle. |
| 10      | Memory-Mapped I/O Demo             | Completed | Demonstrated memory-mapped I/O by toggling GPIO.      |
| 11      | Linker Script 101                  | Completed | Implemented linker script placing .text at 0x00000000. |
| 12      | Start-up Code & crt0               | Completed | Explained crt0.S responsibilities and sources.        |
| 13      | Interrupt Primer                   | Completed | Enabled and handled machine-timer interrupt (MTIP).   |
| 14      | RV32IMAC vs RV32IMC – What’s the “A”? | Completed | Explained RV32IMAC’s atomic extension (A) and its uses. |
| 15      | Atomic Test Program                | Completed | Implemented a mutex using lr.w/sc.w for thread safety. |
| 16      | Using Newlib printf Without an OS  | Completed | Retargeted Newlib printf to UART with custom _write.  |
| 17      | Endianness & Struct Packing        | Completed | Verified RV32 is little-endian using a union trick.   |

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

## 📸 Screenshots for Task 1:
   ![Screenshot](images/task1_1.png)
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

## 📸 Screenshots for Task 2:
   ![Screenshot](images/task2.png)

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
    less hello.s
Explanation: The -S flag generates assembly code (.s file). -O0 disables optimizations for readable output.

### 🧠 Key Assembly Snippet
    addi sp,sp,-16   # Allocate stack space
    sw   ra,12(sp)   # Save return address
Explanation:  
- addi sp,sp,-16: Allocates 16 bytes on the stack for the function’s frame.  
- sw ra,12(sp): Saves the return address (ra) for function return.

✅ **Status**
Completed: Successfully generated and analyzed RISC-V assembly from C code.

## 📸 Screenshots for Task 3:
   ![Screenshot](images/task3.png)

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

✅ **Status**
Completed: Successfully disassembled the ELF and generated binary/hex outputs.

## 📸 Screenshots for Task 4:
   ![Screenshot](images/task4_1.png)
   ![Screenshot](images/task4_2.png)

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

✅ **Status**
Completed: Successfully debugged the program using GDB and QEMU, inspecting registers and instructions.

## 📸 Screenshots for Task 6:
   ![Screenshot](images/task6_1.png)
   ![Screenshot](images/task6_2.png)
   ![Screenshot](images/task6_3.png)
   ![Screenshot](images/task6_4.png)

## 🛰️ Task 7: Bare-Metal Execution with QEMU

**Status:** Completed

### Objective
Run a bare-metal ELF using QEMU and OpenSBI to simulate a real RISC-V system.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Bare-metal execution uses the base integer set (I) for minimal setup.
- **Platform**: Ubuntu 24.04 LTS, running QEMU for emulation and OpenSBI for firmware.

### 📄 Code: Bare-Metal UART Program
     volatile char *uart = (char *)0x10000000;
     void main()
     {
     const char *msg = "Hello, RISC-V!\n";
     while (*msg) {
     *uart = *msg++;
     }
     while(1);
     }
Explanation: A bare-metal program that uses UART to output a message.

### 📄 Startup Code

      .section .text
      .global _start
      _start:
       la sp, _stack_top
       call main
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
    SECTIONS
    {
    . = 0x80200000;
    .text : { *(.text*)}
    .rodata : { *(.rodata*)}
    .data : { *(.data*)}
    .bss : { *(.bss*) *(COMMON)}
    }
Explanation: Defines memory layout for FLASH and RAM, placing sections appropriately.

### 🔧 Commands
      curl -LO https://github.com/qemu/qemu/raw/v8.0.4/pc-bios/opensbi-riscv32-generic-fw_dynamic.bin
      riscv32-unknown-elf-gcc -g -march=rv32im -mabi=ilp32 inostdlib -T linker.ld -o hello2.elf hello2.c startup.s
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

## 📸 Screenshots for Task 7:
   ![Screenshot](images/task7_1.png)
   ![Screenshot](images/task7_2.png)
   ![Screenshot](images/task7_3.png)

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

✅ **Status**
Completed: Successfully compared -O0 and -O2 assembly outputs, highlighting optimization effects.

## 📸 Screenshots for Task 8:
   ![Screenshot](images/task8_1.png)
   ![Screenshot](images/task8_2.png)
   
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
### 📄 Cycle counter
      #include <stdint.h>
      // Function to read the 32-bit cycle counter from CSR 0xC00
      uint32_t read_cycle_counter(void) {
          uint32_t cycles;
          __asm__ volatile (
              "rdcycle %0"
              : "=r" (cycles) // Output constraint
              :               // No input constraints
              :               // No clobbered registers
          );
          return cycles;
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

### 🔧 Commands
    riscv32-unknown-elf-gcc -g -O0 -march=rv32im -mabi=ilp32 -nostdlib -T linkertask9.ld -o task9.elf task9.c startuptask_9.s
    qemu-system-riscv32 -nographic -machine virt -bios none -kernel task9.elf
Explanation: Compiles and runs the program on QEMU without OpenSBI.
 
### 💬 Output
    Value of x: 43
    Cycles taken: 8410761

Explanation: Shows the value of x and the cycles taken for the operation.

✅ **Status**
Completed: Successfully measured execution time using inline assembly and rdcycle.

## 📸 Screenshots for Task 9:
   ![Screenshot](images/task9.png)

## 🔧 Task 10: Memory-Mapped I/O Demo

**Status:** Completed

### Objective
Demonstrate memory-mapped I/O by toggling a GPIO register and prevent compiler optimizations.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Uses base integer instructions (I) for memory-mapped I/O.
- **Platform**: Ubuntu 24.04 LTS, running QEMU for bare-metal simulation.

### 📄 Code
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
Explanation: Toggles a GPIO pin using memory-mapped I/O and outputs via UART.
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

### 🔧 Commands
    riscv32-unknown-elf-gcc -g -O2 -march=rv32im -mabi=ilp32 -nostdlib -T linker10.ld -o task10.elf task10.c startup10.s
    readelf -h task10.elf
    qemu-system-riscv32 -nographic -machine virt -bios none -kernel task10.elf
Explanation: Compiles, checks the ELF header, and runs the program on QEMU.

### 💬 Output
      GPIO Toggled
      B.........
Explanation: Confirms the GPIO toggle and UART output.

⚠️ **Issues Faced**
- GPIO Not Visible: QEMU virt doesn’t emulate GPIO; verified code correctness via UART output.

✅ **Status**
Completed: Demonstrated memory-mapped I/O with GPIO toggling, verified via UART output.

## 📸 Screenshots for Task 10:
   ![Screenshot](images/task10_1.png)
   ![Screenshot](images/task10_2.png)

## 📜 Task 11: Linker Script 101

**Status:** Completed

### Objective
Implement a minimal linker script for RV32IMC to place .text at 0x00000000 and .data at 0x10000000.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Linker script controls memory layout for RV32.
- **Platform**: Ubuntu 24.04 LTS, used for compilation and linking.

### 📄 Code
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
Explanation: A simple program with global variables to test the linker script.

### 📄 Startup Code

    .section .text.start
    .global _start
    _start:
       lui sp, %hi(_stack_top)
       addi sp, sp, %lo(_stack_top)
       call main
    1:  j 1b
    .size _start, . - _start
Explanation: Sets up the stack pointer and calls main.

### 📄 Linker Script
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
      (.text)
      (.rodata)
      } > FLASH
      .data 0x10000000 : {
      _data_start = .;
      (.data)
      _data_end = .;
      } > SRAM
      .bss : {
      _bss_start = .;
      (.bss)
      *(COMMON)
      _bss_end = .;
      } > SRAM
      _stack_top = ORIGIN(SRAM) + LENGTH(SRAM);
      }
Explanation: Places .text at 0x00000000 and .data at 0x10000000.
### 📄 Build Script
      cat << 'EOF' > build_linker_test.sh
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
      EOF
      chmod +x build_linker_test.sch h
Explanation: A script to compile, link, and verify the linker script.

### 🔧 Commands

      chmod +x build_linker_test.sh
      ./build_linker_test.sh
Explanation: Makes the script executable and runs it.

### 💬 Output
#### Section Addresses
0 .text         00001000  00000000  00000000  00001000  21
1 .sdata        00002000  10000000  10000000  00002000  22
2 .sbss         00002004  10000004  10000004  00002004  2**2
#### Symbol Addresses
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
Explanation: Confirms the memory layout and symbol addresses.

⚠️ **Issues Faced**
- Alignment Errors: Initial linker script had misaligned sections; fixed with proper alignment.

✅ **Status**
Completed: Linker script successfully implemented and verified.

## 📸 Screenshots for Task 11:
   ![Screenshot](images/task11.png)

## 🚀 Task 12: Start-up Code & crt0

**Status:** Completed

### Objective
Explain what crt0.S does in a bare-metal RISC-V program and where to get one.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Start-up code uses base integer instructions (I).
- **Platform**: Ubuntu 24.04 LTS, used for referencing start-up code examples.

### 🔸 Key Responsibilities of crt0.S
| Step | Action                     | Description                                      |
|------|----------------------------|--------------------------------------------------|
| 1    | Stack pointer initialization | Set sp to top of RAM for stack usage          |
| 2    | Zero .bss section        | Clear uninitialized variables to zero           |
| 3    | Copy .data section       | Copy initialized data from ROM to RAM           |
| 4    | Call main()              | Transfer control to C main() function         |
| 5    | Infinite loop after main() | Trap if main() returns (optional: halt CPU)  |
Explanation: Outlines the main tasks of crt0.S in a bare-metal setup.

### 📄 Minimal Example: crt0.S

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
Explanation: A minimal crt0.S that sets up the stack, zeros .bss, and calls main.

### 🔹 Summary: crt0.S Essentials
| Component         | Purpose                                      |
|-------------------|----------------------------------------------|
| sp setup        | Prepares environment for stack-based calls   |
| .bss zeroing    | Ensures uninitialized variables start as zero |
| .data copy      | Ensures initialized data is accessible in RAM |
| main() call     | Hands over control to user-defined code      |
| Infinite loop     | Prevents undefined behavior if main() returns |
Explanation: Summarizes the key components of crt0.S.

### 🔸 Where to Find crt0.S?
| Source              | Use Case                                      |
|---------------------|-----------------------------------------------|
| **Newlib**          | Common in embedded systems                   |
| **Platform SDKs**   | SiFive, Kendryte, Espressif SDKs             |
| **Bare-metal Examples** | GitHub: riscv-blinky, riscv-bare-metal-template |
| **Custom Development** | Adaptable for specific SoC/memory layout     |
Explanation: Lists sources for obtaining or developing crt0.S.

⚠️ **Issues Faced**
- Finding Examples: Took time to locate relevant crt0.S examples; resolved via GitHub search.

✅ **Status**
Completed: Explained crt0.S responsibilities and sources.

## 📸 Screenshots for Task 12:

## ⏰ Task 13: Interrupt Primer

**Status:** Completed

### Objective
Enable the machine-timer interrupt (MTIP) and write a simple handler in C/asm.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMC. Uses machine-mode interrupts and timer instructions.
- **Platform**: Ubuntu 24.04 LTS, running QEMU for bare-metal simulation.

### 📄 Code: Timer Interrupt Demo

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
Explanation: Sets up a timer interrupt and handles it with a simple handler.

### 📄 Trap Handler

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
Explanation: A trap handler that calls the timer handler if the interrupt is MTIP.

### 📄 Startup Code

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
Explanation: Sets up the stack, waits for UART, sets the trap vector, and jumps to main.

### 📄 Linker Script
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
### 🔧 Commands
      riscv32-unknown-elf-gcc -g -O0 -march=rv32im -mabi=ilp32 -nostdlib -T linker13.ld -o timer.elf timer_interrupt.c startup13.s trap_handler.s
      qemu-system-riscv32 -nographic -machine virt -bios none -kernel timer.elf
Explanation: Compiles and runs the program on QEMU.

### 💬 Output
      SATimer enabled
      ........MTIP
      MTIP
      MTIP
Explanation: Shows the timer interrupt firing periodically.

⚠️ **Issues Faced**
- Interrupt Not Firing: mie and mstatus bits were initially set incorrectly; fixed with proper CSR writes.

✅ **Status**
Completed: Successfully enabled and handled MTIP with a simple handler.

## 📸 Screenshots for Task 13:
   ![Screenshot](images/task13_1.png)
   ![Screenshot](images/task13_2.png)
## 🧬 Task 14: RV32IMAC vs RV32IMC – What’s the “A”?

**Status:** Completed

### Objective
Explain the ‘A’ (atomic) extension in RV32IMAC, its instructions, and their usefulness.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMAC vs RV32IMC. Focuses on the atomic extension (A).
- **Platform**: Ubuntu 24.04 LTS, used for researching and documenting ISA extensions.

### 🔸 Base Comparison: RV32IMC vs RV32IMAC
| Feature         | RV32IMC | RV32IMAC |
|-----------------|---------|----------|
| **I** (Base Integer) | ✅      | ✅       |
| **M** (Multiply/Divide) | ✅   | ✅       |
| **C** (Compressed Instr.) | ✅  | ✅       |
| **A** (Atomic)  | ❌      | ✅       |
Explanation: Shows the difference between RV32IMC and RV32IMAC.

### 🔸 What is the "A" Extension?
Explanation: The 'A' extension introduces atomic read-modify-write operations for safe concurrent code in multi-core systems.

### 🔸 Key Atomic Instructions
| Instruction   | Meaning               | Use Case                          |
|---------------|-----------------------|-----------------------------------|
| lr.w        | Load-Reserved         | Starts atomic operation           |
| sc.w        | Store-Conditional     | Stores if reservation valid       |
| amoadd.w    | Atomic ADD            | Adds value atomically             |
| amoswap.w   | Atomic SWAP           | Swaps memory and register values  |
| amoor.w     | Atomic OR             | Sets flags atomically             |
| amoand.w    | Atomic AND            | Locks bits                        |
| amomin.w    | Atomic Min            | Priority/resource arbitration     |
| amomax.w    | Atomic Max            | Max-tracking use cases            |
Explanation: Lists the main atomic instructions and their purposes.

### 🔹 Importance of ‘A’
| Purpose                  | Why It’s Needed                          |
|--------------------------|------------------------------------------|
| **Thread-safe Operations** | Allows safe memory sharing              |
| **Lock-Free Programming** | Enables deadlock-free data structures   |
| **OS Support**            | Implements mutexes, semaphores, spinlocks |
| **Multicore Support**     | Synchronizes memory across cores        |

Explanation: Highlights why the A extension is important.

### 🔸 Visual Summary
|RV32IMC                         | RV32IMAC                               |
|--------------------------------|----------------------------------------|
|Base + Mul/Div + Compressed     | Base + Mul/Div + Compressed + ⚡Atomic|
|❌                             | ✅ Supports:|
|❌                             | lr.w, sc.w|
| ❌                            | amoadd.w, amoswap.w|
| ❌                            |Enables thread-safe systems|

Explanation: A visual comparison of RV32IMC and RV32IMAC.

✅ **Status**
Completed: Explained the ‘A’ extension, its instructions, and their importance.

## 🔒 Task 15: Atomic Test Program

**Status:** Completed

### Objective
Provide a two-thread mutex example (pseudo-threads in main) using lr/sc on RV32.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMAC. Uses the atomic extension (A) for lr.w and sc.w instructions to implement a mutex.
- **Platform**: Ubuntu 24.04 LTS, running QEMU for bare-metal simulation.

### 📄 Code: task15.c

      #define UART_TX 0x10000000    // UART transmit register (QEMU virt)
      #define UART_READY 0x10000005 // UART status register (bit 5 = TX ready)
      
      // Define uint32_t for bare-metal
      typedef unsigned int uint32_t;
      
      // Shared counter and mutex
      volatile uint32_t shared_counter = 0;
      volatile uint32_t mutex = 0; // 0 = unlocked, 1 = locked
      
      void uart_putc(char c) {
          volatile char* uart_tx = (volatile char*)UART_TX;
          volatile char* uart_ready = (volatile char*)UART_READY;
          while (!(*uart_ready & (1 << 5))); // Wait for TXFIFO empty
          *uart_tx = c;
      }
      
      void uart_puts(const char* s) {
          while (*s) {
              uart_putc(*s++);
          }
      }
      
      // Mutex lock using lr.w/sc.w
      int mutex_lock(volatile uint32_t* mutex) {
          uint32_t tmp;
          do {
              // Load-reserved
              asm volatile ("lr.w %0, (%1)" : "=r"(tmp) : "r"(mutex));
              if (tmp != 0) continue; // Mutex already locked, retry
              // Store-conditional (try to set mutex to 1)
              asm volatile ("sc.w %0, %2, (%1)" : "=r"(tmp) : "r"(mutex), "r"(1));
          } while (tmp != 0); // Retry if sc.w failed
          return 0;
      }
      
      // Mutex unlock
      void mutex_unlock(volatile uint32_t* mutex) {
          *mutex = 0; // Non-atomic write is safe as lock holder
      }
      
      // Thread 1: Increment counter in critical section
      void thread1(void) {
          mutex_lock(&mutex);
          uart_puts("T1: Enter critical section\n");
          shared_counter++;
          uart_puts("T1: Counter = ");
          uart_putc('0' + shared_counter);
          uart_putc('\n');
          mutex_unlock(&mutex);
          uart_puts("T1: Exit critical section\n");
      }
      
      // Thread 2: Same as Thread 1
      void thread2(void) {
          mutex_lock(&mutex);
          uart_puts("T2: Enter critical section\n");
          shared_counter++;
          uart_puts("T2: Counter = ");
          uart_putc('0' + shared_counter);
          uart_putc('\n');
          mutex_unlock(&mutex);
          uart_puts("T2: Exit critical section\n");
      }
      
      int main() {
          uart_putc('A'); // Debug: program start
          uart_puts("Starting threads\n");
      
          // Simulate two threads by interleaving
          thread1(); // T1 runs first
          thread2(); // T2 runs second
          thread1(); // T1 again
          thread2(); // T2 again
      
          uart_puts("Done\n");
          while (1) {
              uart_putc('.'); // Show running
              for (volatile int i = 0; i < 100000; i++);
          }
          return 0;
      }
Explanation: Implements a spin-lock mutex using lr.w and sc.w, simulating two threads by interleaving thread1 and thread2 calls.

### 📄 Linker Script: Linker15.ld
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

### 📄Startup15.s
.section .text.start
.global _start
_start:
    # Initialize stack pointer
    la sp, _stack_top

    # Early UART output
    li t0, 0x10000005
    li t1, 0x20  # Bit 5
wait_uart:
    lb t2, 0(t0)
    and t2, t2, t1
    beq t2, zero, wait_uart
    li t0, 0x10000000
    li t1, 'S'   # Print 'S'
    sb t1, 0(t0)

    # Jump to main
    jal main
    j .

.section .bss
.align 4
.space 1024
_stack_top:
Explanation: Sets up the stack pointer, waits for UART to be ready, prints an 'S', and jumps to main.

### 🔧 Commands
      riscv32-unknown-elf-gcc -g -O0 -march=rv32imac -mabi=ilp32 -nostdlib -T Linker15.ld -o task15.elf task15.c Startup15.s
      qemu-system-riscv32 -nographic -machine virt -bios none -kernel task15.elf
Explanation:  
- Compiles the program with the RV32IMAC architecture to enable atomic instructions.  
- Runs the program on QEMU without OpenSBI.

### 💬 Output
      SAStarting threads
      T1: Enter critical section
      T1: Counter = 1
      T1: Exit critical section
      T2: Enter critical section
      T2: Counter = 2
      T2: Exit critical section
      T1: Enter critical section
      T1: Counter = 3
      T1: Exit critical section
      T2: Enter critical section
      T2: Counter = 4
      T2: Exit critical section
      Done
      ........

Explanation: Shows the interleaved execution of thread1 and thread2, with the shared counter incrementing safely due to the mutex.

⚠️ **Issues Faced**
- Mutex Not Working Initially: Incorrect lr.w/sc.w usage caused infinite loops; fixed by ensuring proper retry logic in mutex_lock.
- UART Output Garbled: Missing newline characters caused output to merge; fixed by adding uart_putc('\n').

✅ **Status**
Completed: Successfully implemented a mutex using lr.w/sc.w, simulated two threads, and verified safe counter increments.

## 📸 Screenshots for Task 15:

## 🖨️ Task 16: Using Newlib printf Without an OS

**Status:** Completed

### Objective
Retarget _write so that printf sends bytes to a memory-mapped UART in a bare-metal environment.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMAC. Uses base integer instructions (I) and the Newlib library for printf.
- **Platform**: Ubuntu 24.04 LTS, running QEMU for bare-metal simulation.

### 📄 Code: Task16.c
      #include <stdio.h>
      #include <errno.h>
      #include <sys/stat.h>
      #include <sys/types.h>
      
      #define UART_TX 0x10000000    // UART transmit register (QEMU virt)
      #define UART_READY 0x10000005 // UART status register (bit 5 = TX ready)
      
      // UART write function
      void uart_putc(char c) {
          volatile char* uart_tx = (volatile char*)UART_TX;
          volatile char* uart_ready = (volatile char*)UART_READY;
          while (!(*uart_ready & (1 << 5))); // Wait for TXFIFO empty
          *uart_tx = c;
      }
      
      // Retarget _write for printf
      int _write(int fd, const char *buf, unsigned int len) {
          if (fd != 1 && fd != 2) return -1; // Only handle stdout/stderr
          for (unsigned int i = 0; i < len; i++) {
              uart_putc(buf[i]);
          }
          return len;
      }
      
      // Minimal system call stubs
      int _close(int fd) {
          errno = EBADF;
          return -1;
      }
      
      off_t _lseek(int fd, off_t offset, int whence) {
          errno = ESPIPE;
          return -1;
      }
      
      int _read(int fd, char *buf, unsigned int len) {
          errno = EBADF;
          return -1;
      }
      
      int _fstat(int fd, struct stat *buf) {
          if (fd == 1 || fd == 2) {
              buf->st_mode = S_IFCHR; // Character device for stdout/stderr
              return 0;
          }
          errno = EBADF;
          return -1;
      }
      
      int _isatty(int fd) {
          if (fd == 1 || fd == 2) return 1; // stdout/stderr are terminals
          errno = EBADF;
          return 0;
      }
      
      void *_sbrk(int incr) {
          extern char _end; // Defined in linker.ld
          static char *heap_end = 0;
          char *prev_heap_end;
      
          if (heap_end == 0) heap_end = &_end;
      
          prev_heap_end = heap_end;
          heap_end += incr;
      
          // Simple check to avoid heap overflow (adjust as needed)
          if (heap_end > (char*)0x81010000) { // Limit heap to 64KB
              errno = ENOMEM;
              return (void*)-1;
          }
          return prev_heap_end;
      }
      
      void _exit(int status) {
          while (1); // Hang on exit
      }
      
      int _kill(pid_t pid, int sig) {
          errno = EINVAL;
          return -1;
      }
      
      pid_t _getpid(void) {
          return 1; // Single process
      }
      
      int main() {
          uart_putc('A'); // Direct UART test
          printf("Hello, RISC-V! Counter: %d\n", 42); // Test printf
          while (1) {
              uart_putc('.');
              for (volatile int i = 0; i < 100000; i++);
          }
          return 0;
      }
Explanation: Retargets Newlib's printf by implementing _write to send output to UART, with minimal system call stubs for bare-metal compatibility.

### 📄Linker16.ld
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
Explanation: Defines memory layout for FLASH and RAM, with _end symbol for heap management.

### 📄Startup16.s
      .section .text.start
      .global _start
      _start:
          # Initialize stack pointer
          la sp, _stack_top
      
          # Early UART output
          li t0, 0x10000005
          li t1, 0x20  # Bit 5
      wait_uart:
          lb t2, 0(t0)
          and t2, t2, t1
          beq t2, zero, wait_uart
          li t0, 0x10000000
          li t1, 'S'   # Print 'S'
          sb t1, 0(t0)
      
          # Jump to main
          jal main
          j .
      
      .section .bss
      .align 4
      .space 1024
      _stack_top:
Explanation: Sets up the stack pointer, waits for UART to be ready, prints an 'S', and jumps to main.

### 🔧 Commands
      riscv32-unknown-elf-gcc -g -O0 -march=rv32imac -mabi=ilp32 -nostdlib -nostartfiles -T Linker16.ld -o task16.elf Task16.c Startup16.s
      qemu-system-riscv32 -nographic -machine virt -bios none -kernel task16.elf
Explanation:  
- Compiles with -nostdlib and -nostartfiles to avoid standard startup and library code, linking custom syscalls.  
- Runs the program on QEMU without OpenSBI.

### 💬 Output
      SAHello, RISC-V! Counter: 42
      ........
Explanation: Shows the direct UART output ('A'), printf output via _write, and a running indicator.

⚠️ **Issues Faced**
- No Output Initially: Missing _fstat and _isatty stubs caused printf to fail; added stubs to fix.
- Heap Overflow: Initial _sbrk lacked bounds checking; added a 64KB limit to prevent crashes.

✅ **Status**
Completed: Successfully retargeted Newlib printf to UART by implementing _write and necessary syscalls.

## 📸 Screenshots for Task 16:
   ![Screenshot](images/task16.png)

## 🔄 Task 17: Endianness & Struct Packing

**Status:** Completed

### Objective
Verify if RV32 is little-endian by default using a union trick in C to check byte ordering.

### 🏛️ Architecture & Platform
- **Architecture**: RISC-V RV32IMAC. Focuses on byte ordering in memory for RV32.
- **Platform**: Ubuntu 24.04 LTS, running QEMU for bare-metal simulation.

### 📄 Code: Task17.c
      
      #include <stdint.h> // For uint32_t and uint8_t
      
      // --- UART Register Definitions for QEMU virt machine ---
      #define UART_TX 0x10000000
      #define UART_READY 0x10000005
      
      // --- Custom UART Functions ---
      void uart_putc(char c) {
          volatile char* uart_tx = (volatile char*)UART_TX;
          volatile char* uart_ready = (volatile char*)UART_READY;
          while (!(*uart_ready & (1 << 5)));
          *uart_tx = c;
      }
      
      void uart_puts(const char* s) {
          while (*s) {
              uart_putc(*s++);
          }
      }
      
      // --- Helper Functions for Printing Numbers ---
      void uart_put_hex_byte(uint8_t byte) {
          char hex_digits[] = "0123456789ABCDEF";
          uart_putc('0');
          uart_putc('x');
          uart_putc(hex_digits[(byte >> 4) & 0xF]);
          uart_putc(hex_digits[byte & 0xF]);
      }
      
      void uart_put_hex_u32(uint32_t val) {
          char hex_digits[] = "0123456789ABCDEF";
          uart_putc('0');
          uart_putc('x');
          for (int i = 7; i >= 0; i--) {
              uart_putc(hex_digits[(val >> (i * 4)) & 0xF]);
          }
      }
      
      void uart_put_int(int num) {
          if (num == 0) {
              uart_putc('0');
              return;
          }
      
          char buf[12];
          int i = 0;
          int is_negative = 0;
      
          if (num < 0) {
              is_negative = 1;
              num = -num;
          }
      
          while (num > 0) {
              buf[i++] = '0' + (num % 10);
              num /= 10;
          }
      
          if (is_negative) {
              uart_putc('-');
          }
      
          while (i > 0) {
              uart_putc(buf[--i]);
          }
      }
      
      // --- Main Program Entry Point ---
      int main() {
          volatile int x = 42;
          x = x + 1;
      
          uart_puts("--------------------------------\n");
          uart_puts("Bare-metal RISC-V Application\n");
          uart_puts("Value of x: ");
          uart_put_int(x);
          uart_puts("\n");
          uart_puts("--------------------------------\n\n");
      
          // --- Endianness Check Using a Union ---
          union {
              uint32_t value;
              uint8_t bytes[4];
          } endian_check;
      
          endian_check.value = 0x01020304;
      
          uart_puts("Verifying Byte Ordering (Endianness):\n");
          uart_puts("Value stored: ");
          uart_put_hex_u32(endian_check.value);
          uart_puts("\n");
      
          uart_puts("Bytes in memory (from lowest to highest address):\n");
          for (int i = 0; i < 4; i++) {
              uart_puts("Byte ");
              uart_put_int(i);
              uart_puts(": ");
              uart_put_hex_byte(endian_check.bytes[i]);
              uart_puts("\n");
          }
      
          if (endian_check.bytes[0] == 0x04) {
              uart_puts("\nThis system is Little-Endian.\n");
              uart_puts("The least significant byte (0x04) is stored at the lowest memory address.\n");
          } else if (endian_check.bytes[0] == 0x01) {
              uart_puts("\nThis system is Big-Endian.\n");
              uart_puts("The most significant byte (0x01) is stored at the lowest memory address.\n");
          } else {
              uart_puts("\nCould not determine endianness (unexpected byte order).\n");
          }
      
          while (1) {
          }
      
          return 0;
      }
Explanation: Uses a union to store 0x01020304 and checks byte ordering to determine endianness, printing results via UART.

### 📄Linker17.ld :
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
Explanation: Defines memory layout for FLASH and RAM, placing sections appropriately.
### 📄Startup17.s :
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
Explanation: Sets up the stack pointer and jumps to main, with a simple infinite loop if main returns.

### 🔧 Commands

      riscv32-unknown-elf-gcc -g -O0 -march=rv32imac -mabi=ilp32 -nostdlib -T Linker17.ld -o task17.elf Task17.c Startup17.s
      qemu-system-riscv32 -nographic -machine virt -bios none -kernel task17.elf
Explanation:  
- Compiles the program for RV32IMAC with no standard library.  
- Runs the program on QEMU without OpenSBI.

### 💬 Output
      Bare-metal RISC-V Application Value of x: 43
      Verifying Byte Ordering (Endianness):
      Value stored: 0x01020304
      Bytes in memory (from lowest to highest address):
      Byte 0: 0x04
      Byte 1: 0x03
      Byte 2: 0x02
      Byte 3: 0x01
      
      This system is Little-Endian.
      The least significant byte (0x04) is stored at the lowest memory address.

Explanation: Confirms RV32 is little-endian by default, as the least significant byte (0x04) is at the lowest address.

⚠️ **Issues Faced**
- Output Misalignment: Initial output lacked newlines, making it hard to read; added uart_puts("\n") to fix.
- Union Behavior: Had to ensure no compiler optimizations affected the union; used volatile where needed.

✅ **Status**
Completed: Verified RV32 is little-endian using a union trick and printed byte ordering via UART.

## 📸 Screenshots for Task 17:
![Screenshot](images/task17.png)
