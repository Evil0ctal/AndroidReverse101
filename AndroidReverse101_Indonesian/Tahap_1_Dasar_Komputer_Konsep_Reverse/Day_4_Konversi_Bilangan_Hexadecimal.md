# **📜 Day 4: Konversi Bilangan - Mengapa Hexadecimal Penting?**

## **📌 Tujuan Pembelajaran**
✅ Menguasai metode dan formula konversi antara **binary, decimal, hexadecimal**.  
✅ Memahami **mengapa hexadecimal sangat penting dalam komputer, assembly language, dan reverse engineering**.  
✅ Mengenal **struktur file executable ELF**, dan belajar cara menganalisis konten file ELF.  
✅ Melalui **studi kasus praktis**, menguasai peran hexadecimal dalam **memory address, instruction encoding, file structure**, dan **analisis vulnerability**.  

---

# **1️⃣ Gambaran Umum Konversi Bilangan**
### **🔹 Mengapa Perlu Konversi Bilangan?**
Komputer secara internal menggunakan **binary** untuk menyimpan data, tetapi data binary kurang mudah dibaca, oleh karena itu **hexadecimal (Hex)** banyak digunakan untuk merepresentasikan address, machine code, dll.

| **Bilangan** | **Base** | **Range Representasi** | **Contoh** |
|--------|------|---------|---------|
| **Binary** | 2 | `0, 1` | `101010` |
| **Decimal** | 10 | `0-9` | `42` |
| **Hexadecimal** | 16 | `0-9, A-F` | `0x2A` |

---

# **2️⃣ Metode Konversi Bilangan**
### **🔹 Binary ↔ Decimal**
✅ **Binary ke Decimal** (`101010₂` → `42₁₀`):  
**Formula Perhitungan**:
\[
(1 × 2^5) + (0 × 2^4) + (1 × 2^3) + (0 × 2^2) + (1 × 2^1) + (0 × 2^0) = 42
\]

✅ **Decimal ke Binary** (`42₁₀` → `101010₂`):  
**Metode**:
1. `42 ÷ 2 = 21` sisa `0`
2. `21 ÷ 2 = 10` sisa `1`
3. `10 ÷ 2 = 5` sisa `0`
4. `5 ÷ 2 = 2` sisa `1`
5. `2 ÷ 2 = 1` sisa `0`
6. `1 ÷ 2 = 0` sisa `1`
**Hasil: `101010₂`**  

---

# **3️⃣ File Executable ELF**
### **🔹 Apa itu ELF?**
**ELF (Executable and Linkable Format)** adalah **format file executable** yang digunakan sistem Linux dan Unix-like, mirip dengan format file PE (Portable Executable) di Windows.

File ELF bisa berupa:
- **Program executable** (seperti `/bin/ls`)
- **Shared library (file .so)**
- **Core dump**

### **🔹 Konten File ELF**
File ELF terdiri dari beberapa **Segment** dan **Section**, struktur utamanya sebagai berikut:
| **Struktur ELF** | **Fungsi** |
|-------------|------------------------------|
| **ELF Header** | Menyimpan informasi dasar file ELF, seperti magic number, entry address, segment offset dll |
| **Program Header** | Mendeskripsikan cara memuat file ke memori (hanya ada di file executable) |
| **Section Header** | Mendeskripsikan berbagai section, seperti `.text`, `.data`, `.rodata` |

---

# **4️⃣ Parsing Struktur File ELF**
Kita dapat menggunakan perintah `hexdump` untuk melihat konten hexadecimal file ELF:
```bash
hexdump -C /bin/ls | head -n 10
```
Contoh output:
```
00000000  7f 45 4c 46  02 01 01 00  00 00 00 00  00 00 00 00  | .ELF........... |
```
- `7F 45 4C 46` adalah **ELF Magic Number**, digunakan untuk mengidentifikasi file ELF.  

Kita juga dapat menggunakan `readelf` untuk menganalisis struktur ELF:
```bash
readelf -h /bin/ls
```
Contoh output:
```
ELF Header:
  Magic:   7f 45 4c 46
  Class:                             ELF64
  Data:                              2's complement, little endian
  Entry point address:               0x401000
```
- `Entry point address: 0x401000` menunjukkan entry address program.

---

# **5️⃣ Mengapa ELF dan Hexadecimal Penting?**
✅ **ELF Menggunakan Hexadecimal untuk Menyimpan Instruksi**
Semua instruksi CPU pada akhirnya akan dikonversi menjadi **machine code hexadecimal**:
```assembly
mov eax, 5   ; x86 assembly
B8 05 00 00 00 ; Machine code hexadecimal yang sesuai
```
```assembly
MOV R0, #5   ; ARM assembly
E3A00005     ; Machine code hexadecimal yang sesuai
```

✅ **File Executable ELF Mengandung Banyak Data Hexadecimal**
Misalnya, gunakan `objdump` untuk melihat code segment ELF:
```bash
objdump -d /bin/ls | head -n 20
```
Contoh output:
```
0000000000401130 <_start>:
  401130:   48 83 ec 08    sub    rsp,0x8
  401134:   48 8b 05 dd 2e  mov    rax,QWORD PTR [rip+0x2edd]
```
Ini semua adalah **instruction code dari file executable ELF**, merupakan mapping langsung dari **machine code hexadecimal**.

---

# **6️⃣ Tugas Praktik**
### **✅ 1. Latihan Konversi Bilangan**
```python
# Binary -> Decimal
print(int("101010", 2))  # Output: 42

# Decimal -> Hexadecimal
print(hex(42))  # Output: 0x2a

# Hexadecimal -> Binary
print(bin(0x2A))  # Output: 0b101010
```

### **✅ 2. Gunakan `hexdump` untuk Menganalisis File ELF**
```bash
hexdump -C /bin/ls | head -n 10
```

### **✅ 3. Gunakan `readelf` untuk Melihat ELF Header**
```bash
readelf -h /bin/ls
```

### **✅ 4. Decompile File Executable ELF**
```bash
objdump -d /bin/ls | head -n 20
```

---

# **📚 Referensi**
📌 **Tool Konversi Bilangan**
- Website konversi bilangan: [https://www.rapidtables.com/convert/number/](https://www.rapidtables.com/convert/number/)  

📌 **Terkait ELF**
- Manual `readelf`: [https://man7.org/linux/man-pages/man1/readelf.1.html](https://man7.org/linux/man-pages/man1/readelf.1.html)  
- Manual `objdump`: [https://man7.org/linux/man-pages/man1/objdump.1.html](https://man7.org/linux/man-pages/man1/objdump.1.html)  

📌 **CPU Instruction**
- Manual instruksi `x86`: [https://www.felixcloutier.com/x86/](https://www.felixcloutier.com/x86/)  
- Manual instruksi `ARM`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Metode dan formula konversi binary, decimal, hexadecimal.**  
✅ **Struktur dasar file ELF, dan belajar menggunakan `readelf` dan `objdump` untuk analisis.**  
✅ **Menggunakan hexdump untuk melihat header ELF, dan parsing field kunci.**  

🚀 **Langkah Selanjutnya (Day 5)**: **Dasar-Dasar Assembly Language!** 🎯  
