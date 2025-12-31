# **📜 Day 6: x86 vs. ARM (ARM32 & ARM64) - Perbandingan Assembly**

## **📌 Tujuan Pembelajaran**
✅ **Memahami perbedaan inti antara assembly language x86 dan ARM (ARM32 & ARM64)**, termasuk arsitektur, register, instruction set, dan addressing mode.  
✅ **Menguasai perbandingan instruksi x86, AR

M32, dan ARM64**, memahami cara eksekusinya pada arsitektur yang berbeda.  
✅ **Belajar cara menulis kode assembly pada processor x86, ARM32, dan ARM64**, serta melakukan debugging dan analisis decompile.  
✅ **Melalui contoh kode**, menganalisis penerapan praktis assembly pada berbagai arsitektur, termasuk operasi register, function call, penyimpanan data, dll.  
✅ **Memahami perbedaan x86 dan ARM dalam reverse engineering, penelitian keamanan, dan eksploitasi vulnerability**.

---

# **1️⃣ Perbandingan Arsitektur x86 vs. ARM (ARM32 & ARM64)**
| **Karakteristik** | **x86 (CISC)** | **ARM32 (RISC)** | **ARM64 (RISC)** |
|---------|--------------|----------------|----------------|
| **Instruction Set** | **CISC (Complex Instruction Set Computer)** | **RISC (Reduced Instruction Set Computer)** | **RISC (Reduced Instruction Set Computer)** |
| **Panjang Instruksi** | **Variable-length (1-15 byte)** | **Fixed 4 byte** | **Fixed 4 byte** |
| **Addressing Mode** | **Kompleks, mendukung berbagai cara addressing** | **Sederhana, cenderung operasi register** | **Sederhana, cenderung operasi register** |
| **Jumlah Register** | **Sedikit (x86-32: 8 general purpose registers)** | **16 general purpose registers (R0-R15)** | **31 general purpose registers (X0-X30)** |
| **Mode Operasi** | **Real mode, protected mode, long mode (64-bit)** | **AArch32 (32-bit)** | **AArch64 (64-bit)** |
| **Efisiensi Energi** | **High power**, cocok untuk PC dan server | **Low power**, cocok untuk perangkat mobile | **Lebih efisien, cocok untuk mobile dan server** |

📌 **Ringkasan:**
- **x86 mengadopsi arsitektur CISC**, instruksi fleksibel tetapi decoding kompleks.  
- **ARM32 (ARMv7) mengadopsi arsitektur RISC**, instruksi tetap, cocok untuk perangkat mobile 32-bit.  
- **ARM64 (ARMv8) mengoptimalkan arsitektur RISC**, memperkenalkan lebih banyak register, meningkatkan kemampuan komputasi, mendukung OS 64-bit.

---

# **2️⃣ Perbandingan Instruksi x86 vs. ARM32 vs. ARM64**
### **🔹 x86 32/64-bit Registers**
| **Register** | **x86-32 (IA-32)** | **x86-64 (x86-64)** | **Kegunaan** |
|---------|------------|------------|------------|
| EAX/RAX | EAX | RAX | General purpose register (accumulator) |
| EBX/RBX | EBX | RBX | General purpose register (base register) |
| ECX/RCX | ECX | RCX | Counter register (loop) |
| EDX/RDX | EDX | RDX | Data register |
| ESI/RSI | ESI | RSI | Source index register |
| EDI/RDI | EDI | RDI | Destination index register |
| EBP/RBP | EBP | RBP | Stack base pointer |
| ESP/RSP | ESP | RSP | Stack pointer |

### **🔹 ARM32 vs. ARM64 Registers**
| **Register** | **ARM32 (ARMv7)** | **ARM64 (ARMv8)** | **Kegunaan** |
|---------|----------------|----------------|--------|
| General purpose | R0 - R12 | X0 - X30 | Passing parameters, komputasi dan penyimpanan |
| Stack pointer | R13 (SP) | SP | Menunjuk ke stack top |
| Link register | R14 (LR) | X30 (LR) | Menyimpan function return address |
| Program counter | R15 (PC) | PC | Menyimpan current instruction address |

---

### **🔹 Perbandingan Instruksi x86 vs. ARM32 vs. ARM64**
| **Operasi** | **x86 Instruction (CISC)** | **ARM32 Instruction (RISC)** | **ARM64 Instruction (RISC)** |
|---------|-----------------|----------------|----------------|
| Assignment | `mov eax, 5` | `MOV R0, #5` | `MOV X0, #5` |
| Addition | `add eax, 10` | `ADD R0, R0, #10` | `ADD X0, X0, #10` |
| Read memory | `mov eax, [ebx]` | `LDR R0, [R1]` | `LDR X0, [X1]` |
| Store to memory | `mov [ebx], eax` | `STR R0, [R1]` | `STR X0, [X1]` |
| Function call | `call my_function` | `BL my_function` | `BL my_function` |
| Logical operation | `and eax, ebx` | `AND R0, R0, R1` | `AND X0, X0, X1` |
| Conditional jump | `cmp eax, ebx` `je label` | `CMP R0, R1` `BEQ label` | `CMP X0, X1` `B.EQ label` |

---

# **3️⃣ Contoh Kode x86 vs. ARM32 vs. ARM64**
### **🔹 Contoh x86 Assembly**
```assembly
section .text
global _start

_start:
    mov eax, 5      ; Assign 5 ke EAX
    add eax, 10     ; EAX = EAX + 10
    mov ebx, eax    ; Copy EAX ke EBX
    int 0x80        ; Panggil Linux system API
```

---

### **🔹 Contoh ARM32 Assembly**
```assembly
.global _start
_start:
    MOV R0, #5      ; Assign 5 ke R0
    ADD R0, R0, #10 ; R0 = R0 + 10
    LDR R1, [R2]    ; Baca memory yang ditunjuk R2 ke R1
    STR R1, [R3]    ; Simpan R1 ke memory yang ditunjuk R3
    B _start        ; Loop tak terbatas
```

---

### **🔹 Contoh ARM64 Assembly**
```assembly
.global _start
_start:
    MOV X0, #5      ; Assign 5 ke X0
    ADD X0, X0, #10 ; X0 = X0 + 10
    LDR X1, [X2]    ; Baca memory yang ditunjuk X2 ke X1
    STR X1, [X3]    ; Simpan X1 ke memory yang ditunjuk X3
    B _start        ; Loop tak terbatas
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Menjalankan x86 Assembly**
```bash
nasm -f elf64 test.asm
ld -o test test.o
./test
```

### **✅ 2. Menjalankan ARM32 Assembly**
```bash
as -o arm32.o arm32.s
ld -o arm32 arm32.o
./arm32
```

### **✅ 3. Menjalankan ARM64 Assembly**
```bash
as -o arm64.o arm64.s
ld -o arm64 arm64.o
./arm64
```

---

# **📚 Referensi**
📌 **x86 Assembly**
- Manual instruksi `x86`: [https://www.felixcloutier.com/x86/](https://www.felixcloutier.com/x86/)  

📌 **ARM Assembly**
- Manual instruksi `ARM`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Perbedaan inti assembly language x86, ARM32, dan ARM64**  
✅ **Cara menulis dan debugging kode assembly pada berbagai arsitektur**  
✅ **Penerapan praktis assembly dalam reverse engineering**  

🚀 **Langkah Selanjutnya (Day 7)**: **Parsing Instruksi ARM Assembly!** 🎯
