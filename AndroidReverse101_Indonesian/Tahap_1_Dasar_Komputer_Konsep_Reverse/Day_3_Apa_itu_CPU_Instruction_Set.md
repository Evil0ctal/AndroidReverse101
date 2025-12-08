# **📜 Day 3: Apa itu CPU Instruction Set?**
  
## **📌 Tujuan Pembelajaran**
✅ Memahami konsep **CPU Instruction Set**, dan penerapannya pada berbagai arsitektur (x86, ARM, Smali).  
✅ Menguasai karakteristik berbeda dari **x86 (CISC) vs. ARM (RISC) vs. Smali (Android DEX Instruction)**.  
✅ Menguasai perbedaan instruksi **ARMv7 (ARM32) vs. ARMv8 (ARM64)**.  
✅ Memahami **hubungan konversi antara bahasa C dan assembly language (x86, ARM, Smali)**.  
✅ Melalui contoh kode nyata, mengenal penggunaan dasar dari berbagai CPU instruction set.  

---

# **1️⃣ Apa itu CPU Instruction Set?**
**CPU Instruction Set (Instruction Set Architecture, ISA)** adalah kumpulan instruksi software yang dijalankan CPU, menentukan bagaimana CPU memparsing dan mengeksekusi binary code.  

🔹 Fungsi utama:
- Mengontrol tugas komputasi seperti **akses data, operasi aritmatika, operasi logika**.  
- Mempengaruhi **performa, konsumsi daya, kompatibilitas** CPU.  
- Produsen CPU utama mengadopsi **arsitektur instruksi** yang berbeda.  

🔹 **Tiga CPU Instruction Set Utama**
| **Arsitektur** | **Karakteristik** | **Skenario Penerapan** |
|---------|--------|------------|
| **x86 (CISC)** | Complex instruction set, instruksi variable-length, mendukung mode addressing yang powerful | PC, server (Intel, AMD) |
| **ARM (RISC)** | Reduced instruction set, panjang instruksi tetap, low power high performance | Perangkat mobile, embedded (Qualcomm, Apple, Huawei) |
| **Smali (DEX bytecode)** | Instruksi virtual machine Android DEX, berorientasi Java/ART | Android reverse engineering |

---

# **2️⃣ x86 Instruction Set**
**x86 (CISC)** adalah arsitektur yang didorong oleh **Intel dan AMD**, terutama digunakan untuk PC dan server.  
🔹 **Karakteristik:**
- **Panjang instruksi tidak tetap** (1-15 byte), lebih kompleks dari ARM.  
- **Multi-mode addressing**, mendukung operasi yang lebih kompleks.  

### **🔹 Contoh x86 Assembly**
```assembly
section .text
global _start

_start:
    mov eax, 5      ; Assign 5 ke eax
    add eax, 10     ; eax = eax + 10
    sub eax, 2      ; eax = eax - 2
    mov ebx, eax    ; Copy eax ke ebx
    int 0x80        ; Trigger system call
```

🔹 **Karakteristik:**
- `mov eax, 5`: Instruksi transfer data.  
- `add eax, 10`: Operasi penambahan.  
- `int 0x80`: System call di Linux.  

---

# **3️⃣ ARM Instruction Set**
**ARM (RISC)** cocok untuk perangkat mobile, mengadopsi **reduced instruction set (RISC)**.  

🔹 **Karakteristik ARM**
- **Panjang instruksi tetap** (4 byte).  
- **Jumlah register banyak** (mengurangi akses memori, meningkatkan performa).  
- **Low power high efficiency**, cocok untuk perangkat mobile.  

### **🔹 ARM Instruction vs. x86 Instruction**
| **Operasi** | **x86 Instruction (CISC)** | **ARM Instruction (RISC)** |
|---------|-----------------|----------------|
| Assignment | `mov eax, 5` | `MOV R0, #5` |
| Addition | `add eax, 10` | `ADD R0, R0, #10` |
| Read memory | `mov eax, [ebx]` | `LDR R0, [R1]` |
| Store to memory | `mov [ebx], eax` | `STR R0, [R1]` |

---

# **4️⃣ ARMv7 (32-bit) vs. ARMv8 (64-bit)**
🔹 **ARMv7 (ARM32)**
```assembly
.global _start
_start:
    MOV R0, #5      ; Assign 5 ke R0
    ADD R0, R0, #3  ; R0 = R0 + 3
    LDR R1, [R2]    ; Baca memory yang ditunjuk R2 ke R1
    STR R1, [R3]    ; Simpan R1 ke memory yang ditunjuk R3
    B _start        ; Loop tak terbatas
```

🔹 **ARMv8 (ARM64)**
```assembly
.global _start
_start:
    MOV X0, #5      ; Assign 5 ke X0
    ADD X0, X0, #3  ; X0 = X0 + 3
    LDR X1, [X2]    ; Baca memory yang ditunjuk X2 ke X1
    STR X1, [X3]    ; Simpan X1 ke memory yang ditunjuk X3
    B _start        ; Loop tak terbatas
```

📌 **Perbedaan:**
- ARMv7 menggunakan `R0-R15`, ARMv8 menggunakan `X0-X30`.  
- Instruksi ARMv8 mendukung komputasi data 64-bit, meningkatkan kemampuan komputasi.  

---

# **5️⃣ Smali Assembly Language (Android DEX Instruction)**
**Smali adalah assembly language untuk Android DEX, setara dengan versi assembly dari Java bytecode.**  

### **🔹 Contoh Kode Smali**
```smali
.method public static sum(II)I
    .registers 3
    add-int v0, p0, p1
    return v0
.end method
```

🔹 **Poin Kunci Smali**
| **Instruksi** | **Fungsi** |
|---------|--------|
| `add-int v0, p0, p1` | p0 + p1 hasil disimpan di v0 |
| `return v0` | Return hasil komputasi |

### **🔹 Contoh Cracking Smali**
```smali
.method public isVip()Z
    .registers 2
    const/4 v0, 0x1  # Membuat semua user menjadi VIP
    return v0
.end method
```

---

# **6️⃣ C Language vs. Assembly (x86/ARM/Smali)**
### **🔹 Kode C**
```c
int sum(int a, int b) {
    return a + b;
}
```

### **🔹 x86 Assembly**
```assembly
sum:
    mov eax, edi
    add eax, esi
    ret
```

### **🔹 ARM Assembly**
```assembly
sum:
    ADD R0, R0, R1
    BX LR
```

### **🔹 Kode Smali**
```smali
.method public static sum(II)I
    .registers 3
    add-int v0, p0, p1
    return v0
.end method
```

---

# **🛠 Tugas Praktik**
1️⃣ **Cek Arsitektur CPU Perangkat Android**
```bash
cat /proc/cpuinfo
```

2️⃣ **Jalankan x86 Assembly**
```bash
nasm -f elf64 test.asm
ld -o test test.o
./test
```

3️⃣ **Decompile Android APK**
```bash
apktool d myapp.apk -o output_dir
```

---

# **📚 Referensi**
📌 **ARM Instruction Set**
- Dokumentasi resmi ARM: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  

📌 **x86 Instruction Set**
- Manual Intel: [https://software.intel.com/en-us/articles/intel-sdm](https://software.intel.com/en-us/articles/intel-sdm)  

📌 **Tutorial Smali**
- Panduan Smali: [https://github.com/JesusFreke/smali](https://github.com/JesusFreke/smali)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ Perbedaan instruction set x86, ARM, Smali.  
✅ Evolusi ARM32 vs. ARM64.  
✅ Menjalankan dan memodifikasi kode Smali.  

🚀 **Langkah Selanjutnya (Day 4)**: **Konversi Bilangan: Mengapa Hexadecimal Penting?** 🎯  
