# **📜 Day 24: Pengenalan Bahasa Smali**

## **📌 Tujuan Pembelajaran**
✅ Memahami sintaks bahasa Smali (Dalvik bytecode assembly)  
✅ Menguasai register, instruction set, dan control flow Smali  
✅ Mempelajari cara membaca dan menulis kode Smali  
✅ Memahami mapping antara Java code dan Smali code  
✅ Praktik: Modifikasi kode Smali untuk bypass logic aplikasi

---

# **1️⃣ Apa itu Smali?**
Smali adalah bahasa assembly untuk Dalvik bytecode. Setiap file `.java` dikompilasi menjadi `.class`, lalu menjadi `.dex`, yang bisa di-disassemble menjadi `.smali`.

---

# **2️⃣ Sintaks Dasar Smali**

## **Register**
```smali
.locals 2          # Deklarasi 2 local registers (v0, v1)
const/4 v0, 0x1    # v0 = 1
const-string v1, "Hello"  # v1 = "Hello"
```

## **Method Declaration**
```smali
.method public onCreate()V
    .locals 1
    # Method body
    return-void
.end method
```

## **Method Call**
```smali
invoke-virtual {v0}, Landroid/app/Activity;->onCreate()V
```

## **Conditional**
```smali
if-eq v0, v1, :label    # if v0 == v1 goto label
:label
    # Code here
```

---

# **3️⃣ Contoh: Java ke Smali**

**Java Code:**
```java
public boolean isVIP() {
    return false;
}
```

**Smali Code:**
```smali
.method public isVIP()Z
    .locals 1
    const/4 v0, 0x0    # v0 = false
    return v0
.end method
```

**Modified (Hack VIP):**
```smali
.method public isVIP()Z
    .locals 1
    const/4 v0, 0x1    # v0 = true  ← Changed!
    return v0
.end method
```

---

# **🛠 Tugas Praktik**
```bash
# Decompile to Smali
apktool d app.apk -o output/

# Edit Smali
vim output/smali/com/example/MainActivity.smali

# Repackage
apktool b output -o modded.apk

# Sign & Install
jarsigner -keystore my.keystore modded.apk alias
adb install modded.apk
```

---

📚 **Referensi**: [Smali/Baksmali](https://github.com/JesusFreke/smali)

🚀 **Langkah Selanjutnya (Day 25)**: **Eksperimen Modifikasi Kode Smali!** 🎯
