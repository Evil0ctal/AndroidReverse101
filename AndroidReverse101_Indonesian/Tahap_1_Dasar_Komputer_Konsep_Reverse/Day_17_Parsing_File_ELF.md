# **📜 Day 17: Parsing File ELF**

## **📌 Tujuan Pembelajaran**
✅ **Memahami format file ELF (Executable and Linkable Format)**, menguasai struktur header, section, program header.  
✅ **Menguasai cara parse file ELF**, menggunakan tool seperti `readelf`, `objdump`, `hexdump` untuk analisis.  
✅ **Mempelajari cara ekstrak symbol table, function address, informasi dynamic linking dari file ELF**, memahami struktur SO shared library.  
✅ **Menguasai teknik reverse analysis ELF**, termasuk static analysis dan dynamic debugging (GDB, Frida Hook).  
✅ **Menganalisis file ELF Android (`libnative.so`), menguasai teknik Hook dan hijacking**.  

---

# **1️⃣ Dasar-Dasar File ELF**
### **🔹 Apa itu ELF?**
ELF (Executable and Link able Format) adalah format executable file untuk **Linux dan Android**, termasuk:
-**Executable File**
- **Shared Library (Shared Object, .so)**
- **Core Dump**

📌 **Lihat File ELF**
```bash
file libnative.so
```
Contoh output:
```
libnative.so: ELF 64-bit LSB shared object, ARM aarch64
```

📌 **Parse Struktur ELF**
```bash
readelf -h libnative.so
```

---

# **2️⃣ Parsing ELF Header**
### **🔹 ELF Header**
ELF header berisi informasi dasar file ELF, seperti **architecture, entry point, posisi segment table**, dll.

📌 **Lihat ELF Header**
```bash
readelf -h libnative.so
```
Contoh output:
```
ELF Header:
  Magic:   7f 45 4c 46  ...
  Class:   ELF64
  Data:    2's complement, little endian
  Entry point address: 0x0000000000001234
```

📌 **Field Kunci**
| **Field** | **Arti** |
|---------|---------|
| `Magic` | Identifikasi file ELF (7F 45 4C 46 -> .ELF) |
| `Class` | ELF32 / ELF64 |
| `Data` | Little/big endian storage |
| `Entry point` | Program entry address |

---

# **3️⃣ Program Header**
Program Header mendefinisikan **cara loading executable file**, menentukan **code segment, data segment, informasi dynamic linking**.

📌 **Lihat Program Header**
```bash
readelf -l libnative.so
```
Contoh output:
```
Program Headers:
  Type           Offset   VirtAddr   PhysAddr   FileSiz  MemSiz Flags Align
  LOAD           0x000000 0x000000  0x000000  0x1234   0x2000 R E 0x1000
```

📌 **Field Kunci**
| **Field** | **Arti** |
|---------|---------|
| `Type` | LOAD (loadable segment), DYNAMIC (dynamic segment), NOTE (debug info) |
| `VirtAddr` | Virtual address code/data segment |
| `Flags` | R (read), W (write), X (execute) |

---

# **4️⃣ Section Header Table**
Section Header Table mendeskripsikan **berbagai section file ELF (code segment, data segment, symbol table, dll)**.

📌 **Lihat Section Table**
```bash
readelf -S libnative.so
```
Contoh output:
```
Section Headers:
  [Nr] Name          Type         Addr      Off    Size   ES Flg Lk Inf Al
  [ 1] .text         PROGBITS     0x000010  0x0010 0x1000 00  AX  0   0 16
  [ 2] .data         PROGBITS     0x002000  0x2000 0x2000 00  WA  0   0 16
```

📌 **Section Kunci**
| **Section Name** | **Fungsi** |
|---------|---------|
| `.text` | Code segment (read-only, executable) |
| `.data` | Data segment (read-write) |
| `.rodata` | Read-only data segment |
| `.bss` | Uninitialized data |

---

# **5️⃣ Symbol Table**
Symbol Table menyimpan **mapping antara function name, variable name dengan address**, digunakan untuk debugging dan dynamic linking.

📌 **Lihat Symbol Table**
```bash
readelf -s libnative.so | grep " func"
```
Contoh output:
```
  1234: 00000000  123 FUNC  GLOBAL DEFAULT  UND printf
```

📌 **Reverse Lookup Symbol**
```bash
nm -D libnative.so
```

---

# **6️⃣ Informasi Dynamic Linking**
📌 **Lihat Shared Library Dependency**
```bash
ldd libnative.so
```
Contoh output:
```
libc.so => /lib/libc.so
libm.so => /lib/libm.so
```

📌 **Lihat Dynamic Segment**
```bash
readelf -d libnative.so
```

---

# **7️⃣ Reverse ELF**
## **✅ 1. Parse ELF Header**
```bash
readelf -h libnative.so
```
## **✅ 2. Parse Symbol Table**
```bash
nm -D libnative.so
```
## **✅ 3. Disassemble ELF**
```bash
objdump -d libnative.so | head -n 20
```
## **✅ 4. Hook ELF Runtime**
📌 **Gunakan Frida Hook `open()`**
```js
Java.perform(function() {
    var libc = Module.findExportByName(null, "open");
    Interceptor.attach(libc, {
        onEnter: function(args) {
            console.log("File Opened: " + Memory.readUtf8String(args[0]));
        }
    });
});
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Ekstrak ELF**
```bash
adb shell run-as com.example.app cat /data/app/com.example.app/lib/arm64/libnative.so > libnative.so
```
### **✅ 2. Parse ELF**
```bash
readelf -h libnative.so
readelf -S libnative.so
readelf -s libnative.so
```
### **✅ 3. Disassemble**
```bash
objdump -d libnative.so | head -n 20
```
### **✅ 4. Hook `open()`**
```js
Java.perform(function() {
    var libc = Module.findExportByName(null, "open");
    Interceptor.attach(libc, {
        onEnter: function(args) {
            console.log("File Opened: " + Memory.readUtf8String(args[0]));
        }
    });
});
```

---

# **📚 Referensi**
📌 **Parsing ELF**
- Dokumentasi resmi format `ELF`: [https://man7.org/linux/man-pages/man5/elf.5.html](https://man7.org/linux/man-pages/man5/elf.5.html)  
- Manual `readelf`: [https://sourceware.org/binutils/docs/binutils/readelf.html](https://sourceware.org/binutils/docs/binutils/readelf.html)  

📌 **Reverse Analysis**
- Frida Hook `ELF`: [https://frida.re](https://frida.re)  
- `objdump`: [https://sourceware.org/binutils/docs/binutils/](https://sourceware.org/binutils/docs/binutils/)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Cara parse ELF header, program header, symbol table**  
✅ **Cara disassemble ELF dan analisis kode Native**  
✅ **Cara menggunakan Frida/Xposed Hook behavior runtime ELF**  

🚀 **Langkah Selanjutnya (Day 18)**: **Cara Debug Native Layer?** 🎯
