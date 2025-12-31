# **📜 Day 19: Mekanisme Keamanan Android APP**

## **📌 Tujuan Pembelajaran**
✅ **Memahami mekanisme keamanan Android**, termasuk **application sandbox, SELinux, signature verification, Root detection**, dll.  
✅ **Menguasai cara proteksi kode Android APP**, seperti **obfuscation (ProGuard), hardening, dynamic debugging detection**.  
✅ **Mempelajari keamanan storage Android**, menghindari **data leakage (SharedPreferences, SQLite, file storage)**.  
✅ **Menguasai teknik analisis dinamis**, bypass **Root detection, anti-debugging, hardening protection**, meningkatkan kemampuan reverse.  
✅ **Praktik: Menganalisis dan bypass proteksi keamanan Android APP**.

---

# **1️⃣ Application Sandbox Android**
**🔹 Apa itu Application Sandbox?**
- Setiap Android APP berjalan dalam **Linux process independent**, dengan **UID/GID independent**.
- **File, data, process aplikasi berbeda secara default tidak bisa saling akses**.

📌 **Lihat UID Aplikasi**
```bash
adb shell dumpsys package com.example.app | grep userId=
```
Contoh output:
```
userId=10123
```
👉 **Setiap APP memiliki UID unik, mencegah data leakage.**

📌 **Coba Akses Direktori APP Lain**
```bash
adb shell ls /data/data/com.other.app/
```
**Jika tidak Root, akses akan ditolak!**

---

# **2️⃣ SELinux (Mandatory Access Control)**
SELinux (Security-Enhanced Linux) digunakan untuk membatasi **inter-process communication, akses sistem resource**.

📌 **Lihat Status SELinux**
```bash
adb shell getenforce
```
Contoh output:
```
Enforcing  # Menunjukkan SELinux enabled
```
📌 **Disable SELinux (Root Permission)**
```bash
adb shell setenforce 0
```

📌 **Lihat Akses yang Ditolak SELinux**
```bash
adb shell dmesg | grep avc
```

---

# **3️⃣ Signature Verification Android**
Android APP saat install memerlukan **digital signature** untuk memastikan integritas aplikasi.

📌 **Cek Signature APK**
```bash
apksigner verify --print-certs app.apk
```
Contoh output:
```
Signer #1 certificate DN: CN=Developer, O=Example Corp, C=US
```
📌 **Bypass Signature Verification**
Tambahkan di `AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.INSTALL_PACKAGES"/>
```
Kemudian sign ulang:
```bash
zipalign -v 4 modded.apk aligned.apk
apksigner sign --ks my.keystore --out signed.apk aligned.apk
adb install signed.apk
```

---

# **4️⃣ Root Detection**
Banyak aplikasi **mendeteksi apakah device di-Root** untuk mencegah reverse analysis.

📌 **Metode Root Detection Umum**
1. **Cek su**
```java
File f = new File("/system/bin/su");
if (f.exists()) {
    Log.d("RootCheck", "Device is Rooted!");
}
```

2. **Detect Root Process**
```bash
adb shell ps -A | grep magiskd
```
Contoh output:
```
magiskd       1023  567   123456K fg    00000000 S magiskd
```

📌 **Bypass Root Detection**
**Gunakan Frida Hook `File.exists()`**
```js
Java.perform(function() {
    var File = Java.use("java.io.File");
    File.exists.implementation = function() {
        return false;
    };
});
```

📌 **Eksekusi Frida**
```bash
frida -U -n com.example.app -e "..."
```

---

# **5️⃣ Code Obfuscation & Hardening**
### **✅ 1. ProGuard Obfuscation**
```proguard
-dontobfuscate
-keep class com.example.** { *; }
```
📌 **Deobfuscate DEX**
```bash
jadx -d output/ app.apk
```

### **✅ 2. Hardening**
Hardening (seperti `360 Hardening`, `Tencent Hardening`) digunakan untuk **menyembunyikan code logic, mencegah reverse**.

📌 **Cek Apakah APK Di-harden**
```bash
unzip -l app.apk | grep classes
```
Jika `classes.dex` di-split atau diganti, maka **APK mungkin di-harden**.

📌 **Bypass Hardening (Frida Dump DEX)**
```bash
frida -U -n com.example.app -e "Java.perform(function() {
    Java.use('dalvik.system.BaseDexClassLoader').loadClass.implementation = function(name) {
        console.log('Load Class: ' + name);
    };
});"
```

---

# **6️⃣ Keamanan Storage Android**
### **✅ 1. Hindari Plaintext Storage**
❌ **Tidak Aman**
```java
SharedPreferences prefs = getSharedPreferences("settings", MODE_PRIVATE);
prefs.edit().putString("password", "123456").apply();
```
📌 **Lihat Stored Data**
```bash
adb shell cat /data/data/com.example.app/shared_prefs/settings.xml
```
**✅ Solusi: Gunakan Encrypted Storage**
```java
EncryptedSharedPreferences encryptedPrefs = EncryptedSharedPreferences.create(
    "settings",
    MasterKeys.getOrCreate(MasterKeys.AES256_GCM_SPEC),
    context,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
);
encryptedPrefs.edit().putString("password", "123456").apply();
```

---

# **7️⃣ Reverse Bypass Security Mechanism**
## **✅ 1. Bypass Root Detection**
```js
Java.perform(function() {
    var File = Java.use("java.io.File");
    File.exists.implementation = function() {
        return false;
    };
});
```

## **✅ 2. Bypass Anti-Debugging**
```js
Java.perform(function() {
    var Debug = Java.use("android.os.Debug");
    Debug.isDebuggerConnected.implementation = function() {
        return false;
    };
});
```

## **✅ 3. Dump DEX**
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

# **🛠 Tugas Praktik**
### **✅ 1. Cek UID Aplikasi**
```bash
adb shell dumpsys package com.example.app | grep userId=
```
### **✅ 2. Disable SELinux**
```bash
adb shell setenforce 0
```
### **✅ 3. Hook `File.exists()` Bypass Root Detection**
```js
Java.perform(function() {
    var File = Java.use("java.io.File");
    File.exists.implementation = function() {
        return false;
    };
});
```
### **✅ 4. Dump DEX**
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
📌 **Mekanisme Keamanan Android**
- `SELinux`: [https://source.android.com/security/selinux](https://source.android.com/security/selinux)  
- `ProGuard`: [https://developer.android.com/studio/build/shrink-code](https://developer.android.com/studio/build/shrink-code)  
- Mekanisme sign `Android`: [https://developer.android.com/studio/publish/app-signing](https://developer.android.com/studio/publish/app-signing)  

📌 **Reverse Analysis**
- `Frida`: [https://frida.re](https://frida.re)  
- Tutorial Xposed Hook: [https://github.com/rovo89/XposedBridge](https://github.com/rovo89/XposedBridge)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Sandbox mechanism Android, SELinux, metode secure storage**  
✅ **Cara bypass Root detection, signature verification, anti-debugging**  
✅ **Cara Dump DEX, menganalisis code obfuscation dan hardening**  

🚀 **Langkah Selanjutnya (Day 20)**: **CTF Reverse Challenge (Pemula)!** 🎯
