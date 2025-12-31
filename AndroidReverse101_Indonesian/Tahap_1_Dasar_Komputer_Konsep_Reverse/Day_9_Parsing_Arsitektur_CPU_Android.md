# **📜 Day 9: Parsing Arsitektur CPU Android**

## **📌 Tujuan Pembelajaran**
✅ Memahami **arsitektur CPU perangkat Android**, termasuk **ARM32, ARM64, x86, RISC-V**.  
✅ Menguasai **ARMv7, ARMv8 (AArch64) dan fitur utamanya**, memahami **arsitektur big.LITTLE**.  
✅ Mengenal **kompatibilitas CPU perangkat Android**, belajar cara menentukan APK cocok untuk arsitektur mana.  
✅ Melalui **analisis praktis informasi CPU perangkat Android**, mengurai `/proc/cpuinfo` untuk mendapatkan arsitektur perangkat.  
✅ Memahami pengaruh arsitektur CPU yang berbeda terhadap **reverse engineering, optimasi performa, analisis vulnerability**.  

---

# **1️⃣ Arsitektur CPU Mainstream Platform Android**
Saat ini, perangkat Android terutama mengadopsi arsitektur CPU berikut:
| **Arsitektur** | **Instruction Set** | **Common Processor** | **Perangkat yang Cocok** |
|---------|----------|--------------|------------|
| **ARMv7 (32-bit)** | ARM32 | Cortex-A7, Cortex-A15 | Smartphone Android lama, IoT devices |
| **ARMv8 (64-bit)** | ARM64 (AArch64) | Cortex-A53, Cortex-A76 | Smartphone Android flagship modern |
| **x86 / x86_64** | CISC | Intel Atom, AMD Ryzen Embedded | Emulator Android, beberapa tablet |
| **RISC-V** | RISC | Tren masa depan | Perangkat low power, platform Android eksperimental |

---

# **2️⃣ ARMv7 vs. ARMv8 (AArch64)**
| **Karakteristik** | **ARMv7 (32-bit)** | **ARMv8 (64-bit)** |
|---------|----------------|----------------|
| **Instruction Set** | ARM32 (AArch32) | ARM64 (AArch64) |
| **Dukungan Memori Maksimal** | 4GB | 16EB (peningkatan besar) |
| **General Purpose Registers** | 16 (R0-R15) | 31 (X0-X30) |
| **Optimasi Instruction Set** | Bergantung pada `Thumb-2` | Efisien, meningkatkan efisiensi energi |
| **Dukungan NEON SIMD** | Opsional | Built-in support |
| **Performa** | Low power, agak lambat | Efisien, throughput tinggi |
| **Kompatibilitas** | Tidak bisa menjalankan aplikasi 64-bit | Backward compatible dengan kode ARMv7 |

**🔹 Contoh: ARM32 vs. ARM64 Assembly**
```assembly
; Versi ARM32
MOV R0, #5
ADD R0, R0, #3

; Versi ARM64
MOV X0, #5
ADD X0, X0, #3
```
📌 **Versi ARM64 memiliki register yang lebih lebar, kemampuan komputasi lebih kuat!**

---

# **3️⃣ Arsitektur big.LITTLE**
### **🔹 Apa itu big.LITTLE?**
big.LITTLE adalah **arsitektur heterogeneous multi-core** yang dikembangkan ARM, menggabungkan **high-performance cores (big)** dengan **energy-efficient cores (LITTLE)**, mencapai **keseimbangan performa dan konsumsi daya**.

| **Tipe Core** | **Karakteristik** | **Common Cores** |
|------------|--------|-------------|
| **big cores** | High performance, cocok untuk heavy load tasks | Cortex-A76, Cortex-X1 |
| **LITTLE cores** | Low power, cocok untuk background tasks | Cortex-A55 |

📌 **Kombinasi big.LITTLE Umum**
| **CPU Combination** | **Example Processor** |
|------------|------------|
| 4× big + 4× LITTLE | Snapdragon 865 (4× Cortex-A77 + 4× Cortex-A55) |
| 1× big + 3× middle + 4× LITTLE | Snapdragon 8 Gen 1 (1× Cortex-X2 + 3× Cortex-A710 + 4× Cortex-A510) |

**🔹 Bagaimana big.LITTLE Bekerja?**
- Ketika **perangkat idle**, task berjalan di **LITTLE cores**, menghemat daya.
- Ketika **user menjalankan aplikasi besar**, task beralih ke **big cores**, meningkatkan performa.

**💡 Dampak Reverse Engineering**:  
- **Core berbeda menjalankan task berbeda**, saat debugging perlu perhatikan perubahan CPU load.  
- **big cores lebih cocok untuk analisis reverse dengan beban tinggi, seperti Frida, Ghidra.**

---

# **4️⃣ Kompatibilitas Arsitektur Android APK**
APK pada platform Android perlu match dengan arsitektur CPU perangkat. Informasi arsitektur CPU yang didukung APK disimpan di direktori `lib/`, misalnya:
```
lib/arm64-v8a/       # 64-bit ARM
lib/armeabi-v7a/     # 32-bit ARM
lib/x86/             # 32-bit x86
lib/x86_64/          # 64-bit x86
```
📌 **Menentukan Arsitektur CPU yang Didukung Perangkat Android**
```bash
adb shell getprop ro.product.cpu.abi
```
Contoh output:
```
arm64-v8a
```
📌 **Melihat Android `/proc/cpuinfo`**
```bash
adb shell cat /proc/cpuinfo
```
Contoh output:
```
Processor    : ARMv8 Processor rev 3
BogoMIPS    : 38.40
Features    : fp asimd evtstrm aes pmull sha1 sha2 crc32
CPU architecture: 8
```

📌 **Reverse Analysis Target Arsitektur APK**
```bash
unzip -l myapp.apk | grep lib/
```
Contoh output:
```
lib/arm64-v8a/libnative.so
lib/armeabi-v7a/libnative.so
```
👉 Menunjukkan APK ini **mendukung perangkat ARM64 dan ARM32 sekaligus**.

---

# **5️⃣ Reverse Engineering & Analisis Vulnerability**
Arsitektur CPU yang berbeda mempengaruhi analisis reverse:
1. **Instruksi ARM64 lebih ringkas**, tapi **register X0-X30 menambah kompleksitas**.
2. **Scheduling big.LITTLE mempengaruhi analisis dinamis**, saat debugging perlu perhatikan alokasi task CPU core.
3. **Eksploitasi Vulnerability**:
   - **ROP attack (Return Oriented Programming)**: Umum diadvocacy x86, ARM64 karena proteksi `PAC` lebih sulit.
   - **Konversi instruction set**: ARM mendukung **AArch32 & AArch64**, beberapa aplikasi masih menggunakan instruksi ARM32, dapat menyebabkan masalah kompatibilitas.

📌 **Contoh: Menggunakan GDB Membaca `/proc/cpuinfo`**
```bash
adb shell su
gdbserver :5039 --attach <pid>
```
Kemudian di GDB:
```gdb
p *(char(*)[100])0x7ffd8c3b0000
```
📌 **Analisis CPU Instruction**
```bash
objdump -d libnative.so | head -n 20
```
---

# **🛠 Tugas Praktik**
### **✅ 1. Cek Arsitektur CPU Perangkat Android**
```bash
adb shell getprop ro.product.cpu.abi
```
### **✅ 2. Parse `/proc/cpuinfo`**
```bash
adb shell cat /proc/cpuinfo
```
### **✅ 3. Cek Arsitektur CPU yang Kompatibel dengan APK**
```bash
unzip -l myapp.apk | grep lib/
```
### **✅ 4. Decompile Android Shared Library**
```bash
objdump -d libnative.so | head -n 20
```
---

# **📚 Referensi**
📌 **Dokumentasi Resmi ARM**
- Instruction set `ARMv7`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  
- Instruction set `ARMv8 (AArch64)`: [https://developer.arm.com/documentation](https://developer.arm.com/documentation)  

📌 **Referensi big.LITTLE**
- Teknologi `ARM big.LITTLE`: [https://developer.arm.com/solutions/mobile](https://developer.arm.com/solutions/mobile)  

📌 **Android Reverse Engineering**
- `Ghidra`: [https://ghidra-sre.org/](https://ghidra-sre.org/)  
- `IDA Pro`: [https://hex-rays.com/](https://hex-rays.com/)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Arsitektur CPU perangkat Android (ARM32, ARM64, x86, RISC-V)**  
✅ **Cara menganalisis `/proc/cpuinfo` perangkat Android untuk mendapatkan informasi CPU**  
✅ **Cara menentukan APK cocok untuk arsitektur CPU mana**  
✅ **Prinsip kerja arsitektur big.LITTLE dan dampaknya**  

🚀 **Langkah Selanjutnya (Day 10)**: **Parsing Runtime Dalvik vs. ART!** 🎯
