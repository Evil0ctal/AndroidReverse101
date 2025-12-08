# **📜 Day 7: Parsing Instruksi ARM Assembly (ARM32 & ARM64)**

## **📌 Tujuan Pembelajaran**
✅ **Memahami secara mendalam instruction set ARM (ARM32 & ARM64)**, menguasai berbagai penggunaan instruksi umum.  
✅ **Mempelajari klasifikasi instruksi ARM**, termasuk **pemrosesan data, load/store, branch jump, conditional execution, system call**.  
✅ **Membandingkan ARM assembly dengan kode C**, dan menyediakan contoh kode C yang setara.  
✅ **Menulis dan menjalankan kode ARM assembly**, melakukan debugging dan analisis dasar.  
✅ **Memahami penerapan instruksi ARM assembly dalam reverse engineering dan eksploitasi vulnerability**.

---

# **1️⃣ Dasar-Dasar ARM Assembly**  
### **🔹 Mengapa Belajar ARM Assembly?**
- **Arsitek utama platform mobile** (Android, iOS, perangkat embedded).  
- **Low power high performance**, mengadopsi **desain RISC**, digunakan secara luas untuk server, smartphone, dll.  
- **Skill wajib untuk reverse engineering & penelitian keamanan** (seperti cracking APK, analisis debugging, dll).  

### **🔹 Arsitektur Processor ARM**
| **Arsitektur** | **Bit** | **Jumlah Register** | **Panjang Instruksi** | **Representative CPU** |
|---------|------|-------|----------|-----------|
| ARMv7 (ARM32) | 32-bit | 16 (R0-R15) | 4 byte (Thumb 2 byte) | Cortex-A7, Cortex-A15 |
| ARMv8 (ARM64) | 64-bit | 31 (X0-X30) | 4 byte | Cortex-A53, Cortex-A72 |

---

# **2️⃣ Klasifikasi Instruction Set ARM**
### **✅ 1. Instruksi Pemrosesan Data**
Instruksi pemrosesan data digunakan untuk **operasi aritmatika, operasi logika, pergerakan data**.

| **Instruksi** | **Fungsi** | **Contoh ARM32** | **Contoh ARM64** | **Kode C Setara** |
|--------|------|-------------|-------------|-------------|
| `MOV`  | Assignment | `MOV R0, #5` | `MOV X0, #5` | `int a = 5;` |
| `ADD`  | Addition | `ADD R0, R0, #10` | `ADD X0, X0, #10` | `a = a + 10;` |
| `SUB`  | Subtraction | `SUB R0, R0, #2` | `SUB X0, X0, #2` | `a = a - 2;` |
| `MUL`  | Multiplication | `MUL R0, R1, R2` | `MUL X0, X1, X2` | `a = b * c;` |
| `AND`  | Bitwise AND | `AND R0, R0, R1` | `AND X0, X0, X1` | `a = a & b;` |
| `ORR`  | Bitwise OR | `ORR R0, R0, R1` | `ORR X0, X0, X1` | `a = a | b;` |
| `EOR`  | Bitwise XOR | `EOR R0, R0, R1` | `EOR X0, X0, X1` | `a = a ^ b;` |
| `LSL`  | Left shift | `LSL R0, R1, #2` | `LSL X0, X1, #2` | `a = b << 2;` |
| `LSR`  | Right shift | `LSR R0, R1, #2` | `LSR X0, X1, #2` | `a = b >> 2;` |

**🔹 Contoh:**
```assembly
MOV R0, #10  ; R0 = 10
ADD R0, R0, #5  ; R0 = R0 + 5
SUB R1, R0, #2  ; R1 = R0 - 2
MUL R2, R0, R1  ; R2 = R0 * R1
```

Kode **C** yang setara:
```c
int a = 10;
a = a + 5;
int b = a - 2;
int c = a * b;
```

---

## **✅ 2. Instruksi Load/Store**
ARM mengadopsi **arsitektur Load-Store**, semua akses memori harus menggunakan **LDR (load) dan STR (store)**.

| **Instruksi** | **Fungsi** | **Contoh ARM32** | **Contoh ARM64** | **Kode C Setara** |
|--------|------|-------------|-------------|-------------|
| `LDR`  | Baca memori | `LDR R0, [R1]` | `LDR X0, [X1]` | `a = *ptr;` |
| `STR`  | Simpan ke memori | `STR R0, [R1]` | `STR X0, [X1]` | `*ptr = a;` |

**🔹 Contoh:**
```assembly
LDR R0, =0x1000  ; Baca memory address 0x1000
LDR R1, [R0]     ; Baca nilai memory yang ditunjuk R0
STR R1, [R2]     ; Simpan nilai R1 ke memory yang ditunjuk R2
```

Kode **C** yang setara:
```c
int *ptr = (int *)0x1000;
int a = *ptr;
ptr2 = &a;
*ptr2 = a;
```

---

## **✅ 3. Branch dan Control Flow**
ARM menggunakan **conditional jump (B, BL, BX)** dan **loop (CMP + Bxx)** untuk implementasikan control flow.

| **Instruksi** | **Fungsi** | **Contoh ARM32** | **Contoh ARM64** | **Kode C Setara** |
|--------|------|-------------|-------------|-------------|
| `B`    | Unconditional jump | `B loop` | `B loop` | `goto loop;` |
| `BL`   | Procedure call | `BL function` | `BL function` | `function();` |
| `CMP`  | Compare | `CMP R0, R1` | `CMP X0, X1` | `if (a == b) {...}` |
| `BEQ`  | Branch if equal | `BEQ label` | `B.EQ label` | `if (a == b) goto label;` |
| `BNE`  | Branch if not equal | `BNE label` | `B.NE label` | `if (a != b) goto label;` |

**🔹 Contoh:**
```assembly
CMP R0, R1    ; Bandingkan R0 dan R1
BEQ equal     ; Jika sama, jump ke equal
BNE not_equal ; Jika tidak sama, jump ke not_equal
B loop        ; Lanjut loop
```

Kode **C** yang setara:
```c
if (a == b) {
    goto equal;
} else {
    goto not_equal;
}
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Menulis dan Menjalankan ARM Assembly**
```bash
as -o arm.o arm.s
ld -o arm arm.o
./arm
```

### **✅ 2. Decompile ARM ELF**
```bash
objdump -d arm_binary | head -n 20
```

### **✅ 3. Reverse Analysis Android APK**
```bash
apktool d app.apk -o output
vim output/smali/com/example/Main.smali
```

---

# **📚 Referensi**
📌 **Dokumentasi Resmi ARM**
- Instruction set `ARMv7`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  
- Instruction set `ARMv8 (AArch64)`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  

📌 **Reverse Engineering**
- `Ghidra`: [https://ghidra-sre.org/](https://ghidra-sre.org/)  
- `IDA Pro`: [https://hex-rays.com/](https://hex-rays.com/)  

🚀 **Langkah Selanjutnya (Day 8)**: **Function Call dan Return!** 🎯
