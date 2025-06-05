# 🛠️ Task 1 – Install & Sanity-Check the RISC-V Toolchain

## 📋 Task Prompt

**Question:**
> “I have downloaded riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz. How exactly do I unpack it, add it to PATH, and confirm the gcc, objdump, and gdb binaries work?”

**what we should do:**
- Step-by-step `tar -xzf ...` command.
- `export PATH=$HOME/riscv/bin:$PATH` lines for `~/.bashrc`.
- Verification commands:
  - `riscv32-unknown-elf-gcc --version`
  - `riscv32-unknown-elf-objdump --version`
  - `riscv32-unknown-elf-gdb --version`

---

## 🧰 Toolchain Setup Steps

### ✅ 1. Extract the downloaded tar.gz
tar -xzvf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz

### ✅ 2. Move it to $HOME/riscv (optional for consistency)
mv riscv $HOME/riscv

### ✅ 3. Add to your shell PATH
echo 'export PATH=$HOME/riscv/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

### ✅ 4. Version Check Commands
✔️ GCC Version
  ---
- riscv32-unknown-elf-gcc --version

✔️ Objdump Version
  ---
- riscv32-unknown-elf-objdump --version
 
✔️ GDB Version
  ---
- riscv32-unknown-elf-gdb --version
