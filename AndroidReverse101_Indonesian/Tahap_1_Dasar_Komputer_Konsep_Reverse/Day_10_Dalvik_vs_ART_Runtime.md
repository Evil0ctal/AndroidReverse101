# **📜 Day 10: Parsing Runtime Dalvik vs. ART**

## **📌 Tujuan Pembelajaran**
✅ Memahami perbedaan **Dalvik VM** dan **ART (Android Runtime)** serta dampaknya terhadap aplikasi Android.  
✅ Menguasai **format file DEX (Dalvik Executable)**, memahami **cara eksekusi bytecode**.  
✅ Mempelajari **cara ART runtime mengoptimalkan performa Android**, seperti **AOT (Ahead-Of-Time) compilation** dan **JIT (Just-In-Time) compilation**.  
✅ Melalui **analisis behavior Dalvik & ART runtime**, memahami dampaknya terhadap reverse engineering dan optimasi performa.  
✅ Praktik **debugging dan reverse analysis runtime Dalvik dan ART**.

---

# **1️⃣ Apa itu Dalvik dan ART?**
Kode aplikasi Android biasanya ditulis dalam **Java/Kotlin**, setelah dikompilasi menghasilkan **file DEX (Dalvik Executable)**, yang berjalan di perangkat Android.  
**Environment yang menjalankan kode DEX** adalah **Dalvik VM atau ART runtime**.

| **Runtime** | **Karakteristik** | **Android Version** |
|---------|----------------|----------------|
| **Dalvik VM** | Interpreted execution, JIT compilation, register-based | **Android 2.2 - 4.4** |
| **ART (Android Runtime)** | AOT + JIT compilation, runtime optimization, mengurangi beban CPU | **Android 5.0+** |

📌 **Singkatnya**:
- **Dalvik**: Mirip JVM tradisional, setiap kali berjalan perlu interpret code, mempengaruhi performa.  
- **ART**: Memperkenalkan **AOT compilation** (kompilasi saat install menjadi machine code), meningkatkan performa secara signifikan.  

---

# **2️⃣ Perbedaan Inti Dalvik vs. ART**
| **Karakteristik** | **Dalvik VM** | **ART Runtime** |
|---------|------------|------------|
| **Cara Eksekusi Kode** | Interpreted execution + JIT compilation | AOT compilation + JIT compilation |
| **Startup Speed** | Cepat (eksekusi DEX langsung) | Agak lambat (kompilasi OAT saat install pertama) |
| **Runtime Performance** | Rendah (setiap kali run perlu interpret code) | Tinggi (sudah dikompilasi ke native code) |
| **Memory Usage** | Rendah (hanya load bytecode yang diperlukan) | Tinggi (menyimpan file OAT tambahan) |
| **Battery Consumption** | Tinggi (interpretasi code berulang) | Rendah (mengurangi kompilasi runtime) |
| **GC (Garbage Collection)** | Stepwise GC (mempengaruhi kelancaran UI) | Concurrent GC (lebih smooth) |
| **Debugging Support** | Smali-level Hook | Machine code-level Hook (lebih sulit debug) |

---

# **3️⃣ Prinsip Kerja Dalvik**
### **🔹 Format DEX (Dalvik Executable)**
Dalvik runtime mengeksekusi **bytecode DEX**, berbeda dengan file Java `.class` tradisional:
- **Arsitektur register** (bukan stack architecture seperti JVM).
- **Instruction set lebih sedikit**, lebih cocok untuk perangkat mobile.

📌 **Decompile DEX**
```bash
dexdump classes.dex
```
Contoh output:
```
Dex file version 035
magic: dex\n035
class_defs_size: 5
```

📌 **Disassembly Smali**
```bash
baksmali d classes.dex -o output/
```

---

# **4️⃣ Prinsip Kerja ART**
### **🔹 AOT (Ahead-of-Time Compilation)**
ART runtime **saat install aplikasi** melakukan pre-compile `.dex → .oat` (native code), mengurangi overhead runtime.

📌 **Lihat File OAT**
```bash
ls /data/dalvik-cache/
```

📌 **Decompile OAT**
```bash
oatdump --oat-file=/data/dalvik-cache/arm64/system@framework@boot.art
```

### **🔹 JIT (Just-In-Time Compilation)**
- Android 7.0+ **menambahkan JIT di atas AOT**, mengoptimalkan hotspot code secara dinamis, meningkatkan efisiensi runtime.
- Selama eksekusi code, berdasarkan **kondisi runtime** dilakukan **hotspot optimization**, meningkatkan response speed aplikasi.

---

# **5️⃣ Dampak dalam Reverse Engineering**
### **🔹 Reverse Analysis Dalvik**
- Dalvik **berbasis DEX**, dapat langsung decompile **Smali** code.
- Melalui `smali/baksmali` mudah memodifikasi code `.dex`:
```bash
apktool d myapp.apk
vim smali/com/example/MainActivity.smali
```
- Setelah modifikasi, repackage ulang:
```bash
apktool b myapp
jarsigner -verbose -keystore my.keystore myapp/dist/myapp.apk alias_name
```

### **🔹 Reverse Analysis ART**
- ART **langsung mengeksekusi machine code OAT**, tidak bisa dengan mudah memodifikasi Smali code.
- Perlu menggunakan **Frida / GDB / IDA Pro** untuk dynamic debugging:
```bash
frida -U -n myapp -e "Interceptor.attach(Module.findExportByName(null, 'open'), {onEnter: function(args) { console.log('open called'); }})"
```
- ART menggunakan **AOT compilation**, unpacking lebih sulit:
```bash
dd if=/data/app/com.example-1/oat/arm64/base.odex of=/sdcard/base.odex
```

---

# **6️⃣ Dalvik vs. ART pada Perangkat Android**
📌 **Cek Runtime yang Digunakan Perangkat Android**
```bash
adb shell getprop persist.sys.dalvik.vm.lib.2
```
Output:
```
libart.so
```
👉 Menunjukkan perangkat menggunakan **ART runtime**.

📌 **Lihat Direktori OAT**
```bash
adb shell ls /data/dalvik-cache
```
Contoh output:
```
arm64/data@app@com.example-1@base.apk@classes.dex
```
📌 **Cek Execution Mode Aplikasi**
```bash
adb shell getprop dalvik.vm.execution-mode
```
Possible output:
```
jit
aot
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Cek Runtime yang Digunakan Perangkat Android**
```bash
adb shell getprop persist.sys.dalvik.vm.lib.2
```
### **✅ 2. Parse File DEX**
```bash
dexdump classes.dex
baksmali d classes.dex -o output/
```
### **✅ 3. Decompile File ART OAT**
```bash
oatdump --oat-file=/data/dalvik-cache/arm64/system@framework@boot.art
```
### **✅ 4. Hook ART Runtime**
```bash
frida -U -n myapp -e "Interceptor.attach(Module.findExportByName(null, 'open'), {onEnter: function(args) { console.log('open called'); }})"
```

---

# **📚 Referensi**
📌 **Android Runtime**
- Pengenalan `ART`: [https://source.android.com/devices/tech/dalvik](https://source.android.com/devices/tech/dalvik)  
- Struktur `DEX`: [https://source.android.com/devices/tech/dalvik/dex-format](https://source.android.com/devices/tech/dalvik/dex-format)  

📌 **Reverse Engineering**
- `Smali / Baksmali`: [https://github.com/JesusFreke/smali](https://github.com/JesusFreke/smali)  
- `Frida`: [https://frida.re](https://frida.re)  
- `oatdump`: [https://developer.android.com/ndk/guides/other_build_systems](https://developer.android.com/ndk/guides/other_build_systems)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Perbedaan inti runtime Dalvik dan ART**  
✅ **Cara parse file DEX, OAT, melakukan reverse analysis aplikasi**  
✅ **Cara mengecek environment runtime perangkat Android**  
✅ **Cara menggunakan Frida / GDB untuk Hook ART runtime**  

🚀 **Langkah Selanjutnya (Day 11)**: **Parsing Manajemen Process Android!** 🎯
