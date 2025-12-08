# **📜 Day 2: Sejarah dan Perkembangan Android Reverse Engineering**  

## **📌 Tujuan Pembelajaran**  
✅ Memahami **perjalanan perkembangan Android reverse engineering**, dari cracking APK awal hingga teknologi proteksi aplikasi modern.  
✅ Menguasai **persaingan antara proteksi dan cracking APK**, memahami perkembangan hardening dan de-hardening.  
✅ Melalui studi kasus nyata, belajar membandingkan perubahan **mekanisme keamanan Android versi lama vs. baru**.  
✅ Memahami **teknik anti-debugging Android modern**, mempersiapkan analisis reverse untuk tahap selanjutnya.  

---

## **📖 Poin-Poin Pengetahuan**  

### **1️⃣ Perjalanan Perkembangan Android Reverse Engineering**  
Perkembangan Android reverse engineering dapat dibagi menjadi beberapa tahap:  

| **Tahap** | **Waktu** | **Karakteristik** |
|---------|--------|---------|
| **Era Cracking Awal** | 2008 - 2012 | Struktur APK sederhana, bisa bypass verifikasi dengan langsung memodifikasi file `smali`. |
| **Proteksi Obfuscation & Signature** | 2012 - 2015 | Developer mulai menggunakan `ProGuard` untuk code obfuscation, mencegah decompile langsung. |
| **Hardening & Proteksi Dinamis** | 2015 - 2018 | 360, Tencent, Baidu dll meluncurkan solusi hardening, mengadopsi ekstraksi kode dan loading dinamis. |
| **AI & Advanced Security Protection** | 2019 - Sekarang | Android memperkenalkan `Play Protect` dan `SafetyNet`, tingkat kesulitan reverse meningkat. |

---

### **2️⃣ Metode Reverse Android Awal (2008 - 2012)**
#### **Contoh 1: Cracking VIP Membership**
Sekitar tahun 2010, banyak APP menggunakan logika sederhana `if (isVip) {}` untuk menentukan apakah user adalah VIP.  

**🔹 Metode Cracking Awal**
- Gunakan `Apktool` untuk decompile APK:  
  ```bash
  apktool d my_app.apk -o output_dir
  ```
- Modifikasi kode `smali`, ubah method `isVip()`:
  ```smali
  .method public isVip()Z
      .registers 2
      const/4 v0, 0x1  # Membuat semua user menjadi VIP
      return v0
  .end method
  ```
- Repackage dan sign ulang:
  ```bash
  apktool b output_dir -o my_hacked_app.apk
  java -jar signapk.jar cert.pem key.pk8 my_hacked_app.apk signed.apk
  ```
- Install APK yang sudah dimodifikasi, dapat bypass pembatasan VIP.

---

### **3️⃣ Perkembangan Proteksi Aplikasi Android (2012 - 2018)**  
🔹 **Code Obfuscation (ProGuard & R8)**  
- `ProGuard` mulai populer, struktur kode menjadi tidak terbaca:
  ```java
  public class a {
      public void b() { System.out.println("Hello!"); }
  }
  ```
  **Setelah decompile:**
  ```java
  public class a {
      public void a() { System.out.println("Hello!"); }
  }
  ```

🔹 **Teknologi Hardening (Dex Encryption & Code Extraction)**
- Kode tidak disimpan langsung dalam `classes.dex`, tetapi dimuat secara dinamis saat runtime.  
- Contohnya setelah hardening 360, `classes.dex` mungkin berubah menjadi `libshell.so`, perlu dump untuk restore.  

🔹 **Perlawanan Analisis Dinamis (Anti-Debugging & Anti-Frida)**  
- Tool Hook awal (seperti `Frida`) dapat dengan mudah memodifikasi logika APP, tetapi APP modern mungkin mendeteksi Frida:
  ```java
  public static boolean isFridaDetected() {
      return Class.forName("frida.Agent") != null;
  }
  ```

---

### **4️⃣ Teknik Anti-Debugging Android Modern (2018 - Sekarang)**
🔹 **SafetyNet & Play Protect**  
- Google memperkenalkan **Play Protect** di Android 8.0 dan sistem yang lebih baru, menggunakan AI untuk mendeteksi aplikasi berbahaya, mencegah Root dan Hook.  
- `SafetyNet` mendeteksi environment simulasi, perangkat yang di-Root, sistem yang dimodifikasi.  

🔹 **Teknik Anti-Reverse Modern**
| **Teknologi Keamanan** | **Fungsi** |
|-------------|---------|
| **Code Obfuscation (ProGuard/R8)** | Membuat kode setelah decompile sulit dibaca |
| **DEX Encryption** | Membuat `classes.dex` tidak terbaca |
| **ShellCode Dynamic Loading** | Kode tidak disimpan langsung dalam file `dex`, tetapi di-decrypt saat runtime |
| **Anti-Debugging** | Mendeteksi debugger (seperti GDB, Frida) dan menghentikan process |
| **Anti-Hook** | Mendeteksi injeksi `Frida` atau `Xposed` |

---

## **🛠 Tugas Praktik**
1️⃣ **Analisis APK Era Awal**
- Download APK **sekitar tahun 2012** (seperti UC Browser versi lama).  
- Gunakan `apktool` untuk decompile, lihat `AndroidManifest.xml` dan kode `smali`.  
- Coba modifikasi method `isVip()`, bypass verifikasi.  

2️⃣ **Analisis APP Modern**
- Pilih **APP modern** (seperti WeChat, TikTok, Taobao), gunakan `jadx` untuk decompile.  
- Amati apakah menggunakan `ProGuard` atau `R8` untuk code obfuscation.  
- Gunakan `Frida` untuk mendeteksi apakah bisa Hook logika APP.  

---

## **📚 Referensi**
📌 **Tool Reverse Engineering Android**
- `Apktool`: [https://github.com/iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool)  
- `Frida`: [https://frida.re](https://frida.re)  
- `Ghidra`: [https://ghidra-sre.org](https://ghidra-sre.org)  
- `Jadx`: [https://github.com/skylot/jadx](https://github.com/skylot/jadx)  
- `Xposed`: [https://repo.xposed.info/module/de.robv.android.xposed.installer](https://repo.xposed.info/module/de.robv.android.xposed.installer)  

📌 **Artikel Terkait**
- 《Android Reverse Engineering: From Beginner to Mastery》  
- Pengenalan Google Play Protect: [https://support.google.com/googleplay/answer/2812853](https://support.google.com/googleplay/answer/2812853)  
- Forum Reverse Engineering: [https://reverseengineering.stackexchange.com](https://reverseengineering.stackexchange.com)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ Sejarah perkembangan Android reverse engineering dan evolusi teknologi utama.  
✅ Memahami strategi proteksi keamanan Android modern.  
✅ Dapat menggunakan `apktool` untuk decompile APK, dan menganalisis strukturnya.  

🚀 **Langkah Selanjutnya (Day 3)**: **Apa itu CPU Instruction Set?** 🎯  
