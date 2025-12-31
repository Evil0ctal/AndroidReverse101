# **📜 Day 12: Parsing Mekanisme Permission Android**

## **📌 Tujuan Pembelajaran**
✅ **Memahami mekanisme manajemen permission Android**, termasuk **normal permission, dangerous permission, special permission**.  
✅ **Menguasai model runtime permission Android 6.0+**, mempelajari **flow request permission dinamis**.  
✅ **Mempelajari cara query, modify, bypass permission check di perangkat Android**.  
✅ **Reverse analysis permission call aplikasi, memahami cara Frida / Xposed Hook permission verification**.  
✅ **Menganalisis peran SELinux dalam manajemen permission Android**.

---

# **1️⃣ Gambaran Umum Model Permission Android**
Android mengadopsi **model keamanan berbasis permission**, aplikasi saat mengakses data sensitif (seperti camera, lokasi, telepon), harus **declare dan mendapat permission**.

**📌 Klasifikasi Permission**
| **Tipe Permission** | **Deskripsi** | **Contoh** |
|------------|------|------|
| **Normal Permission** | Low risk, otomatis diberikan saat install | `INTERNET`, `ACCESS_NETWORK_STATE` |
| **Dangerous Permission** | Melibatkan privasi, user perlu otorisasi manual | `READ_CONTACTS`, `CAMERA`, `LOCATION` |
| **Special Permission** | Hanya system app atau app dengan signature tertentu | `MANAGE_EXTERNAL_STORAGE`, `SYSTEM_ALERT_WINDOW` |

📌 **Lihat Semua Android Permission**
```bash
adb shell pm list permissions
```

---

# **2️⃣ Model Runtime Permission Android 6.0+**
📌 **Android 6.0 (API 23) memperkenalkan runtime permission**, dangerous permission perlu **request dinamis**.

**📌 Contoh: Request Permission `CAMERA`**
```java
if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CAMERA}, 100);
}
```
**📌 Listen Permission Result**
```java
@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    if (requestCode == 100 && grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
        Log.d("Permission", "Camera permission granted!");
    }
}
```

---

# **3️⃣ Command Manajemen Permission Android**
📌 **Lihat Permission yang Diberikan ke Aplikasi**
```bash
adb shell dumpsys package com.example.app | grep permission
```

📌 **Grant/Revoke Permission Aplikasi (perlu root)**
```bash
adb shell pm grant com.example.app android.permission.CAMERA
adb shell pm revoke com.example.app android.permission.CAMERA
```

📌 **Query Mode SELinux Aktif**
```bash
adb shell getenforce
```
Output:
```
Enforcing  # SELinux enabled
```

📌 **Disable SELinux (Root Permission)**
```bash
adb shell setenforce 0
```

---

# **4️⃣ Bypass Permission Verification**
## **✅ 1. Hook Permission Check**
📌 **Gunakan Frida Bypass `checkSelfPermission`**
```js
Java.perform(function() {
    var ActivityCompat = Java.use("androidx.core.app.ActivityCompat");
    ActivityCompat.checkSelfPermission.implementation = function(context, permission) {
        console.log("Bypassing checkSelfPermission: " + permission);
        return 0;  // Langsung return PERMISSION_GRANTED
    };
});
```
📌 **Eksekusi Command Frida**
```bash
frida -U -n com.example.app -e "..."
```

---

## **✅ 2. Modifikasi `AndroidManifest.xml` untuk Bypass Permission**
**Reverse analysis APK dan modifikasi permission**
```bash
apktool d app.apk -o decompiled/
vim decompiled/AndroidManifest.xml
```
Modifikasi:
```xml
<uses-permission android:name="android.permission.CAMERA"/>
```
Repackage ulang:
```bash
apktool b decompiled -o modded.apk
jarsigner -verbose -keystore my.keystore modded.apk alias_name
adb install modded.apk
```

---

## **✅ 3. Bypass Runtime Permission**
**Gunakan Xposed Module Hook `requestPermissions`**
```java
findAndHookMethod("android.app.Activity", lpparam.classLoader, "requestPermissions",
    String[].class, int.class, new XC_MethodHook() {
        @Override
        protected void beforeHookedMethod(MethodHookParam param) {
            Log.d("Xposed", "Bypassing requestPermissions");
            param.setResult(null);
        }
    }
);
```

---

# **5️⃣ Manajemen Permission Android SELinux**
### **🔹 Fungsi SELinux**
SELinux adalah mekanisme **Mandatory Access Control (MAC)** Android:
- **Enforcing mode** (default): Blokir akses tidak terotorisasi.
- **Permissive mode**: Hanya catat log, tidak intercept akses.

📌 **Query SELinux Policy**
```bash
adb shell dmesg | grep avc
```

📌 **Hook SELinux untuk Bypass Permission**
```c
int selinux_android_setcon(const char *context) {
    return 0;  // Bypass SELinux access control
}
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Query Semua Permission di Perangkat**
```bash
adb shell pm list permissions
```
### **✅ 2. Lihat Permission Aplikasi**
```bash
adb shell dumpsys package com.example.app | grep permission
```
### **✅ 3. Reverse Analysis Permission APK**
```bash
apktool d app.apk -o decompiled/
vim decompiled/AndroidManifest.xml
```
### **✅ 4. Hook `checkSelfPermission` Bypass Permission Check**
```js
Java.perform(function() {
    var ActivityCompat = Java.use("androidx.core.app.ActivityCompat");
    ActivityCompat.checkSelfPermission.implementation = function(context, permission) {
        console.log("Bypassing checkSelfPermission: " + permission);
        return 0;
    };
});
```

---

# **📚 Referensi**
📌 **Dokumentasi Permission Android**
- Panduan permission resmi: [https://developer.android.com/guide/topics/permissions/](https://developer.android.com/guide/topics/permissions/)  
- Runtime permission Android: [https://developer.android.com/training/permissions/requesting](https://developer.android.com/training/permissions/requesting)  

📌 **Reverse Engineering**
- Frida Hook Android: [https://frida.re](https://frida.re)  
- Tutorial Xposed Hook: [https://github.com/rovo89/XposedBridge](https://github.com/rovo89/XposedBridge)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Model permission Android (normal, dangerous, special permission)**  
✅ **Cara query, modify, bypass permission Android**  
✅ **Cara menggunakan Frida / Xposed Hook Android permission check**  
✅ **Peran SELinux dalam keamanan Android**  

🚀 **Langkah Selanjutnya (Day 13)**: **Parsing Struktur Direktori Android APP!** 🎯
