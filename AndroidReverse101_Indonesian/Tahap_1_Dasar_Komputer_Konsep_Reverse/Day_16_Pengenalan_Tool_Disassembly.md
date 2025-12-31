# **📜 Day 16: Pengenalan Tool Disassembly**

## **📌 Tujuan Pembelajaran**
✅ **Memahami konsep dasar disassembly**, menguasai cara mengkonversi binary file menjadi kode assembly yang dapat dibaca.  
✅ **Mengenal tool disassembly mainstream** (IDA Pro, Ghidra, Radare2, objdump) beserta fungsi dan skenario penggunaannya.  
✅ **Mempelajari cara menganalisis file Android ELF, file DEX, Native shared library (.so)**.  
✅ **Menguasai metode analisis dinamis berbasis Frida / Xposed**, Hook function kunci.  
✅ **Melalui operasi praktis, ekstrak, decompile, analisis binary code aplikasi Android**.  

---

# **1️⃣ Dasar-Dasar Disassembly**
### **🔹 Apa itu Disassembly?**
Disassembly adalah proses mengkonversi **machine code (Binary Code) menjadi assembly code**, sehingga dapat memahami logika eksekusi program.

📌 **Contoh**
| **Binary Instruction** | **ARM Assembly** | **Kode C Setara** |
|-------------|-----------|-----------|
| `E3A00005` | `MOV R0, #5` | `int a = 5;` |
| `E0801001` | `ADD R1, R0, R1` | `b = a + b;` |
| `EB000001` | `BL function` | `function();` |

📌 **Lihat Arsitektur File ELF**
```bash
file libnative.so
```
Contoh output:
```
libnative.so: ELF 64-bit LSB shared object, ARM aarch64
```

---

# **2️⃣ Tool Disassembly**
## **✅ 1. objdump (Built-in Linux)**
📌 **Disassemble File ELF**
```bash
objdump -d libnative.so | head -n 20
```
📌 **Parse Informasi Segment ELF**
```bash
readelf -h libnative.so
```

---

## **✅ 2. IDA Pro (Interactive Disassembler)**
📌 **Install IDA Free**
```bash
wget https://out7.hex-rays.com/files/idafree83_linux.run
chmod +x idafree83_linux.run
./idafree83_linux.run
```
📌 **Buka File ELF**
1. **File → Open → Pilih `libnative.so`**
2. **Pilih CPU architecture (ARM/ARM64)**
3. **Mulai analisis, lihat function table, strings, cross-references (XREF)**

📌 **Shortcut Keys**
| **Operasi** | **Shortcut Key** |
|---------|---------|
| Cross-reference | `X` |
| Toggle assembly/C code | `F5` |
| Find strings | `Shift + F12` |

---

## **✅ 3. Ghidra (NSA Open Source Tool)**
📌 **Install Ghidra**
```bash
wget https://ghidra-sre.org/ghidra_10.1.5_PUBLIC_20231005.zip
unzip ghidra_10.1.5_PUBLIC_20231005.zip
cd ghidra_10.1.5
./ghidraRun
```
📌 **Analisis File ELF**
1. **File → New Project → Import `libnative.so`**
2. **Pilih ARM processor**
3. **Double-click function, lihat hasil disassembly**
4. **Gunakan `Decompiler` untuk restore kode C**

📌 **Ghidra Shortcut Keys Umum**
| **Operasi** | **Shortcut Key** |
|---------|---------|
| Find strings | `Ctrl + Shift + F` |
| Cross-reference | `Ctrl + Shift + X` |
| Decompile | `F4` |

---

## **✅ 4. Radare2 (Open Source CLI Disassembly Tool)**
📌 **Install Radare2**
```bash
git clone https://github.com/radareorg/radare2.git
cd radare2
sys/install.sh
```
📌 **Disassemble ELF**
```bash
r2 -AA libnative.so
```
📌 **Command Umum**
| **Command** | **Fungsi** |
|---------|---------|
| `aa` | Auto analysis |
| `afl` | Tampilkan semua function |
| `pdf @ main` | Disassemble `main()` |
| `izz` | Find strings |

---

# **3️⃣ Reverse Analysis Android ELF**
## **✅ 1. Ekstrak libnative.so**
```bash
adb shell run-as com.example.app cat /data/app/com.example.app/lib/arm64/libnative.so > libnative.so
```

## **✅ 2. Parse ELF Header**
```bash
readelf -h libnative.so
```

## **✅ 3. Disassemble**
```bash
objdump -d libnative.so | head -n 20
```

## **✅ 4. Hook ELF Runtime Behavior**
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
📌 **Eksekusi**
```bash
frida -U -n com.example.app -e "..."
```

---

# **4️⃣ Hook Kode DEX**
📌 **Gunakan Frida Hook DEX Runtime**
```js
Java.perform(function() {
    var DexClassLoader = Java.use("dalvik.system.DexClassLoader");
    DexClassLoader.loadClass.implementation = function(name) {
        console.log("Hooked Dex Load: " + name);
        return this.loadClass(name);
    };
});
```

📌 **Eksekusi**
```bash
frida -U -n com.example.app -e "..."
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Gunakan `objdump` Disassemble ELF**
```bash
objdump -d libnative.so | head -n 20
```
### **✅ 2. Gunakan IDA Pro/Ghidra Decompile ELF**
1. **Import `libnative.so`**
2. **Find strings**
3. **Analisis function kunci**
### **✅ 3. Hook `open()` Call**
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
### **✅ 4. Hook `DexClassLoader`**
```js
Java.perform(function() {
    var DexClassLoader = Java.use("dalvik.system.DexClassLoader");
    DexClassLoader.loadClass.implementation = function(name) {
        console.log("Hooked Dex Load: " + name);
        return this.loadClass(name);
    };
});
```

---

# **📚 Referensi**
📌 **Tool Disassembly**
- `IDA Pro`: [https://hex-rays.com/](https://hex-rays.com/)  
- `Ghidra`: [https://ghidra-sre.org/](https://ghidra-sre.org/)  
- `Radare2`: [https://github.com/radareorg/radare2](https://github.com/radareorg/radare2)  

📌 **Android ELF Reverse**
- `Frida`: [https://frida.re](https://frida.re/)  
- `objdump`: [https://sourceware.org/binutils/docs/binutils/](https://sourceware.org/binutils/docs/binutils/)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Penggunaan tool disassembly mainstream (IDA, Ghidra, objdump, Radare2)**  
✅ **Cara parse file ELF, menganalisis kode Native Android**  
✅ **Cara menggunakan Frida/Xposed untuk analisis dinamis**  

🚀 **Langkah Selanjutnya (Day 17)**: **Parsing File ELF!** 🎯
