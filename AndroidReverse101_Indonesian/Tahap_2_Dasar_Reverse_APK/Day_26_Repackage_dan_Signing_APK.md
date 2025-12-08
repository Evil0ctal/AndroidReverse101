# **📜 Day 26: Repackage dan Signing APK**

## **📌 Tujuan Pembelajaran**
✅ Memahami proses repackaging APK setelah modifikasi  
✅ Menguasai Android APK signing mechanism  
✅ Mempelajari cara generate keystore dan sign APK  
✅ Memahami zipalign untuk optimasi APK  
✅ Praktik: Complete workflow dari modify hingga install

---

# **1️⃣ Proses Repackaging**

## **Langkah 1: Decompile**
```bash
apktool d original.apk -o decompiled/
```

## **Langkah 2: Modify**
```bash
# Edit Smali, resources, manifest, etc.
vim decompiled/smali/com/example/MainActivity.smali
```

## **Langkah 3: Repackage**
```bash
apktool b decompiled/ -o unsigned.apk
```

---

# **2️⃣ APK Signing**

## **Generate Keystore**
```bash
keytool -genkey -v -keystore my-release-key.keystore \
    -alias alias_name \
    -keyalg RSA \
    -keysize 2048 \
    -validity 10000
```

## **Sign APK (Method 1: jarsigner)**
```bash
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 \
    -keystore my-release-key.keystore \
    unsigned.apk alias_name
```

## **Sign APK (Method 2: apksigner)**
```bash
apksigner sign --ks my-release-key.keystore \
    --out signed.apk unsigned.apk
```

## **Verify Signature**
```bash
jarsigner -verify -verbose -certs signed.apk
# or
apksigner verify signed.apk
```

---

# **3️⃣ Zipalign Optimization**
```bash
# Zipalign before signing (recommended)
zipalign -v -p 4 unsigned.apk aligned.apk
apksigner sign --ks my-release-key.keystore \
    --out final.apk aligned.apk
```

---

# **4️⃣ Install Modified APK**
```bash
# Uninstall original
adb uninstall com.example.app

# Install modified
adb install final.apk
```

---

# **🛠 Tugas Praktik - Complete Workflow**
```bash
# 1. Decompile
apktool d app.apk -o dec/

# 2. Modify (e.g., change app name)
echo '<string name="app_name">Hacked App</string>' > dec/res/values/strings.xml

# 3. Repackage
apktool b dec/ -o un signed.apk

# 4. Zipalign
zipalign -v 4 unsigned.apk aligned.apk

# 5. Sign
apksigner sign --ks my.keystore --out final.apk aligned.apk

# 6. Verify
apksigner verify final.apk

# 7. Install
adb install final.apk
```

---

📚 **Referensi**: 
- [apksigner](https://developer.android.com/studio/command-line/apksigner)
- [zipalign](https://developer.android.com/studio/command-line/zipalign)

🚀 **Langkah Selanjutnya (Day 27)**: **Pengenalan Dynamic Debugging!** 🎯
