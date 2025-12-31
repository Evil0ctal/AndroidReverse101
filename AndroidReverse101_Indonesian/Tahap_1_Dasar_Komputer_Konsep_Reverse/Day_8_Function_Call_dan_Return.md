# **📜 Day 8: Function Call dan Return (ARM32 & ARM64)**

## **📌 Tujuan Pembelajaran**
✅ **Memahami mekanisme function call dan return pada arsitektur ARM32 dan ARM64**.  
✅ **Menguasai cara passing parameter function ARM** (register & stack).  
✅ **Belajar cara memanggil dan return function dalam assembly**, serta berinteraksi dengan bahasa C.  
✅ **Menguasai ARM calling convention (AAPCS)**, memahami fungsi instruksi `BL`, `BX`, `RET`.  
✅ **Melalui debugging dan disassembly, menganalisis cara function call dalam ARM assembly**.

---

# **1️⃣ Prinsip Dasar Function Call**
### **🔹 Apa itu Function Call?**
Function Call adalah proses **jumping ke code segment lain untuk mengeksekusi tugas tertentu, dan return setelah selesai**.  
Dalam arsitektur ARM, function call terutama melibatkan:
1. **Passing parameters (Registers & Stack)**
2. **Menyimpan return address (Link Register, LR)**
3. **Mengeksekusi function code**
4. **Return ke caller**

---

# **2️⃣ ARM32 vs. ARM64 Calling Convention**
### **🔹 ARM32 (AAPCS Convention)**
Pada ARM32 (AArch32):
- **4 parameter pertama disimpan di register R0-R3**, parameter sisanya disimpan di stack.
- **Return value disimpan di R0 (integer) atau R0-R1 (64-bit integer/float)**.
- **Sebelum memanggil function, instruksi `BL` otomatis menyimpan return address ke `LR (R14)`**.

### **🔹 ARM64 (AAPCS Convention)**
Pada ARM64 (AArch64):
- **8 parameter pertama disimpan di register X0-X7**, parameter sisanya disimpan di stack.
- **Return value disimpan di X0 (integer) atau X0-X1 (64-bit integer/float)**.
- **Saat memanggil function, instruksi `BL` otomatis menyimpan return address ke `X30 (LR)`**.

---

# **3️⃣ Instruksi Function Call**
| **Instruksi** | **Fungsi** | **Contoh ARM32** | **Contoh ARM64** | **Kode C Setara** |
|--------|------|-------------|-------------|-------------|
| `BL`  | Call sub-function | `BL func` | `BL func` | `func();` |
| `BX`  | Return to caller | `BX LR` | N/A | `return;` |
| `RET` | Return to caller | N/A | `RET` | `return;` |

---

# **4️⃣ Contoh Function Call ARM32 & ARM64**
### **🔹 Contoh ARM32**
```assembly
.global _start

_start:
    MOV R0, #5      ; Passing parameter a = 5
    MOV R1, #3      ; Passing parameter b = 3
    BL add_numbers  ; Call add_numbers(R0, R1)
    B _start        ; Loop tak terbatas

add_numbers:
    ADD R0, R0, R1  ; R0 = R0 + R1
    BX LR           ; Return to caller
```

**Kode C yang setara**:
```c
int add_numbers(int a, int b) {
    return a + b;
}

int main() {
    int result = add_numbers(5, 3);
    while (1);
}
```

---

### **🔹 Contoh ARM64**
```assembly
.global _start

_start:
    MOV X0, #5      ; Passing parameter a = 5
    MOV X1, #3      ; Passing parameter b = 3
    BL add_numbers  ; Call add_numbers(X0, X1)
    B _start        ; Loop tak terbatas

add_numbers:
    ADD X0, X0, X1  ; X0 = X0 + X1
    RET             ; Return to caller
```

**Kode C yang setara**:
```c
long add_numbers(long a, long b) {
    return a + b;
}

int main() {
    long result = add_numbers(5, 3);
    while (1);
}
```

---

# **5️⃣ Contoh Function Call Kompleks**
### **🔹 Passing Multiple Parameters**
ARM32:
```assembly
MOV R0, #5
MOV R1, #3
MOV R2, #2
MOV R3, #4
BL complex_func
```

ARM64:
```assembly
MOV X0, #5
MOV X1, #3
MOV X2, #2
MOV X3, #4
BL complex_func
```

Kode C setara:
```c
int complex_func(int a, int b, int c, int d);
complex_func(5, 3, 2, 4);
```

---

### **🔹 Recursive Function Call (Factorial)**
**ARM32**
```assembly
.global factorial

factorial:
    CMP R0, #1
    BLE end_factorial
    PUSH {R0, LR}
    SUB R0, R0, #1
    BL factorial
    POP {R1, LR}
    MUL R0, R0, R1
end_factorial:
    BX LR
```

**ARM64**
```assembly
.global factorial

factorial:
    CMP X0, #1
    BLE end_factorial
    STP X0, LR, [SP, #-16]!
    SUB X0, X0, #1
    BL factorial
    LDP X1, LR, [SP], #16
    MUL X0, X0, X1
end_factorial:
    RET
```

Kode C setara:
```c
int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```

---

# **6️⃣ Stack Management**
**ARM32**
```assembly
PUSH {R4, LR}   ; Simpan register
POP {R4, PC}    ; Restore dan return
```

**ARM64**
```assembly
STP X29, X30, [SP, #-16]!  ; Proteksi X29, X30
LDP X29, X30, [SP], #16    ; Restore X29, X30
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

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Mekanisme function call dan return ARM32 & ARM64**  
✅ **Cara menulis dan debugging function call di platform ARM**  
✅ **Penerapan praktis ARM assembly dalam reverse engineering**  

🚀 **Langkah Selanjutnya (Day 9)**: **Parsing Arsitektur CPU Android!** 🎯
