# **📜 Day 5: Dasar-Dasar Assembly Language**

## **📌 Tujuan Pembelajaran**
✅ Memahami konsep dasar **Assembly Language** dan perannya dalam arsitektur komputer.  
✅ Mempelajari dasar-dasar dari tiga assembly language: **x86 Assembly, ARM Assembly, Smali (DEX bytecode)**.  
✅ Menguasai konsep inti assembly language seperti **register, format instruksi, transfer data, operasi aritmatika, control flow**.  
✅ Melalui **contoh praktis**, belajar bagaimana assembly berinteraksi dengan bahasa C, dan mengimplementasikan program assembly sederhana.  
✅ Mempelajari **peran assembly dalam reverse engineering dan eksploitasi vulnerability**, serta melakukan debugging dan analisis sederhana.

---

# **1️⃣ Apa itu Assembly Language?**
**Assembly Language** adalah **bahasa pemrograman tingkat rendah**, yang menggunakan **mnemonic** untuk menggantikan instruksi binary, memungkinkan programmer mengontrol hardware komputer dengan lebih mudah.  

| **Tingkat Bahasa** | **Contoh** |
|---------|----------------------|
| **Bahasa Tingkat Tinggi (C, Python)** | `int a = 5;` |
| **Assembly Language (x86, ARM)** | `MOV R0, #5` |
| **Machine Code (Instruksi Hexadecimal)** | `B8 05 00 00 00` |

Assembly language terkait erat dengan instruction set CPU tertentu, seperti:
- **x86 assembly** (Processor Intel, AMD)
- **ARM assembly** (Perangkat mobile, embedded)
- **Smali assembly** (Android DEX bytecode)

---

# **2️⃣ Dasar-Dasar x86 Assembly**
### **🔹 Arsitektur Processor x86**
x86 mengadopsi **CISC (Complex Instruction Set Computer)**, mendukung **instruksi variable-length** dan **mode addressing yang kaya**.

**x86 32-bit (IA-32) Registers**
| **Register** | **Kegunaan** |
|---------|------------|
| EAX | Accumulator (operasi/return value) |
| EBX | Base register |
| ECX | Counter register (loop) |
| EDX | Data register |
| ESI | Source index register |
| EDI | Destination index register |
| EBP | Stack base pointer |
| ESP | Stack pointer |

### **🔹 Contoh Instruksi x86**
```assembly
section .text
global _start

_start:
    mov eax, 5      ; EAX = 5
    add eax, 10     ; EAX = EAX + 10
    sub eax, 2      ; EAX = EAX - 2
    int 0x80        ; System call
```
📌 **Karakteristik**:
- `mov eax, 5`: Assign 5 ke `eax`.  
- `add eax, 10`: Operasi penambahan pada `eax`.  
- `int 0x80`: Memanggil Linux system API.

---

# **3️⃣ Dasar-Dasar ARM Assembly**
### **🔹 Arsitektur Processor ARM**
ARM mengadopsi **RISC (Reduced Instruction Set Computer)**, panjang instruksi tetap, efisiensi eksekusi lebih tinggi, digunakan secara luas untuk **perangkat mobile dan embedded system**.

**ARMv7 (ARM32) Registers**
| **Register** | **Kegunaan** |
|---------|--------|
| R0-R3 | Passing function parameters |
| R4-R11 | General purpose registers |
| R12 | Procedure call register |
| R13 | Stack pointer (SP) |
| R14 | Link register (LR) |
| R15 | Program counter (PC) |

**ARMv8 (ARM64) Registers**
| **Register** | **Kegunaan** |
|---------|--------|
| X0-X7 | Passing parameters dan return value |
| X8 | System call |
| X9-X15 | Temporary variables |
| X19-X30 | General purpose registers |
| X30 (LR) | Link register |

### **🔹 Contoh Instruksi ARM**
```assembly
.global _start
_start:
    MOV R0, #5      ; Assign 5 ke R0
    ADD R0, R0, #3  ; R0 = R0 + 3
    LDR R1, [R2]    ; Baca memory yang ditunjuk R2 ke R1
    STR R1, [R3]    ; Simpan R1 ke memory yang ditunjuk R3
    B _start        ; Loop tak terbatas
```

---

# **4️⃣ Smali (Android DEX Assembly)**
**Smali adalah assembly language untuk file Android DEX (Dalvik Executable)**, setara dengan versi assembly dari Java bytecode.

### **🔹 Contoh Kode Smali**
```smali
.method public static sum(II)I
    .registers 3
    add-int v0, p0, p1
    return v0
.end method
```

### **🔹 Contoh Cracking Smali**
```smali
.method public isVip()Z
    .registers 2
    const/4 v0, 0x1  # Membuat semua user jadi VIP
    return v0
.end method
```
📌 **Modifikasi kode Smali dapat digunakan untuk bypass pembatasan VIP aplikasi Android**.

---

# **5️⃣ Interaksi antara Assembly dan Bahasa C**
### **🔹 Bahasa C Memanggil x86 Assembly**
```c
#include <stdio.h>

int add(int a, int b) {
    int result;
    __asm__ (
        "addl %%ebx, %%eax;"
        : "=a" (result)
        : "a" (a), "b" (b)
    );
    return result;
}

int main() {
    printf("Result: %d\n", add(5, 10));
    return 0;
}
```

### **🔹 Bahasa C Memanggil ARM Assembly**
```c
int add(int a, int b) {
    int result;
    __asm__ (
        "ADD %0, %1, %2"
        : "=r" (result)
        : "r" (a), "r" (b)
    );
    return result;
}
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Menulis dan Menjalankan x86 Assembly**
```bash
nasm -f elf64 test.asm
ld -o test test.o
./test
```

### **✅ 2. Menjalankan ARM Assembly**
```assembly
.global _start
_start:
    MOV R0, #1
    LDR R1, =message
    MOV R2, #13
    MOV R7, #4
    SWI 0
    MOV R7, #1
    SWI 0

.section .data
message: .ascii "Hello, ARM!\n"
```

### **✅ 3. Decompile APK dan Modifikasi Smali**
```bash
apktool d app.apk -o output
vim output/smali/com/example/Main.smali
```

---

# **📚 Referensi**
📌 **x86 Assembly**
- Manual instruksi `x86`: [https://www.felixcloutier.com/x86/](https://www.felixcloutier.com/x86/)  
- Manual `NASM`: [https://nasm.us](https://nasm.us)  

📌 **ARM Assembly**
- Manual instruksi `ARM`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  
- Panduan assembly `AArch64`: [https://azeria-labs.com/](https://azeria-labs.com/)  

📌 **Smali**
- Tutorial `Smali`: [https://github.com/JesusFreke/smali](https://github.com/JesusFreke/smali)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Pengetahuan dasar assembly language x86, ARM, Smali.**  
✅ **Cara assembly berinteraksi dengan bahasa C.**  
✅ **Menjalankan, memodifikasi, dan debugging kode assembly.**  

🚀 **Langkah Selanjutnya (Day 6)**: **Perbandingan x86 vs. ARM Assembly!** 🎯  
