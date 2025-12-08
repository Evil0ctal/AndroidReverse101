# **📜 Day 34: Obfuscation dan Deobfuscation Kode Android**

## **📌 Tujuan Pembelajaran**
✅ Memahami code obfuscation techniques  
✅ Menguasai ProGuard/R8 configuration  
✅ Mempelajari cara deobfuscate code  
✅ Memahami mapping files dan retrace  
✅ Praktik: Analyze obfuscated code

---

# **1️⃣ Apa itu Obfuscation?**
Obfuscation membuat code sulit dibaca dengan:
- Rename classes/methods (a.b.c())
- Remove debug info
- String encryption
- Control flow obfuscation

---

# **2️⃣ ProGuard/R8**
```proguard
# proguard-rules.pro
-keep class com.example.** { *; }
-dontshrink
-dontoptimize
```

---

# **3️⃣ Deobfuscation**
```bash
# Find mapping file
unzip app.apk META-INF/proguard/mapping.txt

# Retrace stack trace
retrace mapping.txt crash.txt
```

---

# **🛠 Praktik**
- Analyze obfuscated APK
- Find original class names
- Use mapping files

🚀 **Next (Day 35)**: **Reverse Algoritma Enkripsi!** 🎯
