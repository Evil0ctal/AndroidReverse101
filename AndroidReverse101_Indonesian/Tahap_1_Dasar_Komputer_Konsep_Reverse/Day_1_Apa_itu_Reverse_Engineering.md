### **📜 Day 1: Apa itu Reverse Engineering**  

#### **📌 Tujuan Pembelajaran**  
✅ Memahami apa itu **Reverse Engineering**, dan mengetahui skenario penerapannya.  
✅ Memahami perbedaan antara **Reverse Engineering** dan **Forward Engineering**.  
✅ Mengenal konsep dasar **Software Reverse Engineering**, seperti **Analisis Statis** dan **Analisis Dinamis**.  
✅ Mengetahui tool reverse engineering yang umum digunakan, dan menginstal environment dasar.  

---  

#### **📖 Poin-Poin Pengetahuan**  

### **1️⃣ Apa itu Reverse Engineering?**  
Reverse Engineering (Rekayasa Balik, disingkat RE) adalah proses menganalisis **struktur, fungsi, dan prinsip implementasi** dari sistem yang sudah ada untuk memahami cara kerjanya.  

🔹 Inti dari reverse engineering adalah **membongkar dan memahami**, tidak hanya berlaku untuk software, tetapi juga digunakan secara luas dalam **hardware, protokol jaringan, penelitian keamanan, analisis model AI, dll**.  

### **2️⃣ Skenario Penerapan Reverse Engineering**
| **Bidang**      | **Penerapan** |
|-------------|--------------------------------------------------|
| **Analisis Software** | Reverse APP, software cracking, reverse protocol, debugging API |
| **Penelitian Keamanan** | Analisis malware, vulnerability hunting, analisis virus, pengujian keamanan Web |
| **Analisis Hardware** | Analisis chip, desain PCB, cracking perangkat IoT |
| **Reverse AI** | Parsing model AI, ekstraksi weight, optimisasi inference |
| **Reverse Game** | Pengembangan game cheat, ekstraksi resource, analisis packet jaringan |

**🌟 Kasus 1: Software Cracking**  
- Anda mengunduh **aplikasi berbayar**, tetapi tidak memiliki lisensi. Anda dapat menggunakan reverse engineering untuk menganalisis **logika verifikasi**, dan mencoba mem-bypass-nya.  

**🌟 Kasus 2: Analisis Protokol**  
- Sebuah APP hanya mendukung akses API melalui client resmi, Anda dapat menganalisis **struktur API** melalui reverse engineering, lalu menulis kode sendiri untuk memanggilnya.  

### **3️⃣ Reverse Engineering vs. Forward Engineering**
| | **Forward Engineering** | **Reverse Engineering** |
|------|----------------|----------------|
| **Cara Berpikir** | Merancang dan membangun | Membongkar dan menganalisis |
| **Tujuan** | Mengembangkan produk dari 0 sampai 1 | Memahami produk yang sudah ada |
| **Skenario Penerapan** | Pengembangan software, desain sistem | Cracking, vulnerability hunting, optimasi |
| **Tool** | IDE (VS Code, Android Studio) | Tool decompile, debugger |

### **4️⃣ Analisis Statis vs. Analisis Dinamis**
| | **Analisis Statis** | **Analisis Dinamis** |
|-------------|----------------------------------|--------------------------------|
| **Metode** | Melihat langsung kode file, struktur | Menjalankan program, memantau behavior |
| **Tool** | Tool decompile (jadx, Ghidra) | Debugger (Frida, GDB, LLDB) |
| **Keunggulan** | Analisis cepat, menghindari trigger anti-debugging | Environment nyata, observasi dinamis |
| **Kelemahan** | Kadang tidak bisa langsung melihat logika runtime | Mungkin memicu proteksi anti-debugging |

---

#### **🛠 Tugas Praktik**
1️⃣ **Install Tool Reverse Engineering Dasar**  
🔹 Download dan install **jadx** (decompile APK), analisis struktur `classes.dex`.  
🔹 Install **Frida** (Hook tool), coba Hook script Python sederhana.  
🔹 Install **Ghidra / IDA Free**, buka dan lihat file ELF.  

2️⃣ **Analisis APK Sederhana**
- Download file **APK** apapun (misalnya `Calculator.apk`).  
- Gunakan `jadx` untuk decompile APK tersebut, lihat kode `MainActivity.java`.  

---

#### **📚 Referensi**
📌 **Tool Reverse Engineering Android**  
- `jadx`: [https://github.com/skylot/jadx](https://github.com/skylot/jadx)  
- `Frida`: [https://frida.re](https://frida.re)  
- `Ghidra`: [https://ghidra-sre.org](https://ghidra-sre.org)  
- `APKTool`: [https://github.com/iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool)  

📌 **Bacaan yang Direkomendasikan**
- 《Android Software Security and Reverse Analysis》  
- 《The Art of Reverse Engineering》  
- Blog Reverse Engineering: [https://reverseengineering.stackexchange.com](https://reverseengineering.stackexchange.com)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**
✅ Konsep inti reverse engineering  
✅ Perbedaan antara Reverse Engineering vs. Forward Engineering  
✅ Instalasi dan penggunaan tool dasar  

🚀 **Langkah Selanjutnya (Day 2)**: **Sejarah dan Perkembangan Android Reverse Engineering** 🎯  
