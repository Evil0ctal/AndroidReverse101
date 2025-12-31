# **📜 Day 15: Menulis Kode ARM Assembly (Eksperimen)**

## **📌 Tujuan Pembelajaran**
✅ **Mempelajari sintaks assembly ARM32 & ARM64**, menguasai instruction set dasar (MOV, ADD, SUB, LDR, STR, BL, CMP, B).  
✅ **Memahami struktur register ARM** (general purpose registers, stack pointer, program counter).  
✅ **Menulis dan menjalankan kode ARM assembly**, menggunakan `as` (GNU Assembler) dan `ld` untuk assembly dan linking.  
✅ **Menguasai calling convention ARM assembly di platform Linux**, berinteraksi dengan kode C.  
✅ **Debugging program ARM assembly**, menggunakan `gdb` untuk single-step tracing.  

---

# **1️⃣ Dasar-Dasar ARM Assembly**
### **🔹 Register Utama**
| **Arsitektur** | **General Purpose Registers** | **Special Registers** |
|---------|--------------|--------------|
| **ARM32** | R0 - R12 | SP (R13), LR (R14), PC (R15) |
| **ARM64** | X0 - X30 | SP, LR (X30), PC |

- `SP` (Stack Pointer): Stack pointer
- `LR` (Link Register): Menyimpan function return address
- `PC` (Program Counter): Program counter

📌 **Lihat Register**
```bash
adb shell cat /proc/cpuinfo
```

---

# **2️⃣ Instruksi Dasar**
| **Instruksi** | **Fungsi** | **Contoh ARM32** | **Contoh ARM64** |
|--------|------|-------------|-------------|
| `MOV`  | Assignment | `MOV R0, #5` | `MOV X0, #5` |
| `ADD`  | Addition | `ADD R0, R0, #10` | `ADD X0, X0, #10` |
| `SUB`  | Subtraction | `SUB R0, R0, #2` | `SUB X0, X0, #2` |
| `MUL`  | Multiplication | `MUL R0, R1, R2` | `MUL X0, X1, X2` |
| `LDR`  | Read memory | `LDR R0, [R1]` | `LDR X0, [X1]` |
| `STR`  | Store memory | `STR R0, [R1]` | `STR X0, [X1]` |
| `CMP`  | Compare | `CMP R0, R1` | `CMP X0, X1` |
| `B`    | Unconditional jump | `B label` | `B label` |
| `BL`   | Function call | `BL func` | `BL func` |
| `RET`  | Return | `BX LR` | `RET` |

📌 **Contoh**
```assembly
MOV R0, #10
ADD R0, R0, #5
SUB R1, R0, #2
MUL R2, R0, R1
```

---

# **3️⃣ Contoh Kode ARM Assembly**
### **✅ 1. Hello World (ARM32)**
```assembly
.global _start
.section .data
msg:    .asciz "Hello, ARM!\n"
len = . - msg

.section .text
_start:
    MOV R0, #1          @ File descriptor 1 (standard output)
    LDR R1, =msg        @ Load string address
    LDR R2, =len        @ Load string length
    MOV R7, #4          @ Call write() system call
    SWI 0               @ Trigger system call

    MOV R7, #1          @ Call exit() system call
    SWI 0
```
📌 **Compile dan Run**
```bash
as -o hello.o hello.s
ld -o hello hello.o
./hello
```

---

### **✅ 2. Addition Function (ARM64)**
```assembly
.global add_numbers
add_numbers:
    ADD X0, X0, X1  @ X0 = X0 + X1
    RET             @ Return
```
📌 **Kode C yang Setara**
```c
long add_numbers(long a, long b) {
    return a + b;
}
```

---

# **4️⃣ ARM Assembly Memanggil Kode C**
📌 **Kode Assembly (ARM64)**
```assembly
.global _start
_start:
    MOV X0, #5
    MOV X1, #3
    BL add_numbers
    B _start

.global add_numbers
add_numbers:
    ADD X0, X0, X1
    RET
```
📌 **Kode C yang Setara**
```c
long add_numbers(long a, long b) {
    return a + b;
}
```

📌 **Compile dan Run**
```bash
as -o add.o add.s
ld -o add add.o
./add
```

---

# **5️⃣ Debugging ARM Assembly**
📌 **Gunakan GDB untuk Debugging**
```bash
gdb ./hello
```
📌 **Command Debugging Umum**
```gdb
disassemble _start     # Disassembly
info registers         # Lihat register
break _start          # Set breakpoint
run                   # Run program
stepi                 # Single-step execution
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Tulis dan Jalankan Hello World**
```bash
as -o hello.o hello.s
ld -o hello hello.o
./hello
```
### **✅ 2. Tulis dan Panggil Addition Function**
```bash
as -o add.o add.s
ld -o add add.o
./add
```
### **✅ 3. Debug ARM Assembly**
```bash
gdb ./hello
```

---

# **📚 Referensi**
📌 **Panduan ARM Assembly**
- Dokumentasi resmi `ARM`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  
- Instruction set `ARM`: [https://developer.arm.com/architectures/instruction-sets](https://developer.arm.com/architectures/instruction-sets)  

📌 **Tool Debugging**
- Debugging ARM dengan `GDB`: [https://sourceware.org/gdb/](https://sourceware.org/gdb/)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Cara menulis kode ARM assembly dan menjalankannya**  
✅ **Cara berinteraksi dengan kode C**  
✅ **Cara debugging program ARM assembly**  

🚀 **Langkah Selanjutnya (Day 16)**: **Pengenalan Tool Disassembly!** 🎯
