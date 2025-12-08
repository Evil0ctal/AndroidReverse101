# **📜 Day 23: Parsing Struktur File DEX**

## **📌 Tujuan Pembelajaran**
✅ Memahami format file DEX (Dalvik Executable) secara mendalam  
✅ Menguasai struktur header, string pool, type list, method table DEX  
✅ Mempelajari cara parse dan analisis file DEX menggunakan tool  
✅ Memahami perbedaan DEX dengan Java class file tradisional  
✅ Praktik: Ekstrak, parse, dan modifikasi file DEX

---

# **1️⃣ Apa itu File DEX?**
DEX (Dalvik Executable) adalah format bytecode untuk Android, dioptimalkan untuk mobile devices dengan memory terbatas.

📌 **Lihat File DEX**
```bash
dexdump -f classes.dex | head -n 50
```

---

# **2️⃣ Struktur DEX**
File DEX terdiri dari:
- **Header**: Magic number, checksum, file size
- **String Pool**: Semua string dalam aplikasi
- **Type List**: Daftar tipe data
- **Proto List**: Method prototypes
- **Field List**: Field definitions
- **Method List**: Method definitions
- **Class Def**: Class definitions
- **Data Section**: Code bytecode aktual

---

# **3️⃣ Tools Parsing DEX**
```bash
# Dump DEX structure
dexdump classes.dex

# Decompile ke Java
jadx -d output/ classes.dex

# Decompile ke Smali
baksmali d classes.dex -o smali/
```

---

# **🛠 Tugas Praktik**
```bash
# Extract DEX
unzip app.apk classes.dex

# Analyze structure
dexdump -f classes.dex

# Decompile
jadx -d output/ classes.dex
```

📚 **Referensi**: [DEX Format](https://source.android.com/devices/tech/dalvik/dex-format)

🚀 **Langkah Selanjutnya (Day 24)**: **Pengenalan Bahasa Smali!** 🎯
