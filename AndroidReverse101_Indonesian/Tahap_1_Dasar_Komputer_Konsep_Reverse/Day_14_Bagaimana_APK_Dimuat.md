# **📜 Day 14: Bagaimana APK Dimuat**

## **📌 Tujuan Pembelajaran**
✅ **Memahami flow loading aplikasi Android**, dari install APK hingga eksekusi Dalvik/ART.  
✅ **Menguasai mekanisme inti seperti parsing APK, loading DEX, konversi OAT, class loader (ClassLoader)**.  
✅ **Mempelajari cara menganalisis behavior runtime APK di perangkat Android**, menguasai command `pm`, `am`, `dumpsys package`.  
✅ **Meneliti teknologi dynamic loading (DexClassLoader, PathClassLoader) dan penerapannya dalam reverse engineering**.  
✅ **Menggunakan Frida/Xposed Hook flow loading APK, melakukan analisis keamanan & anti-debugging**.  

---

# **1️⃣ Gambaran Umum Flow Loading APK**
APK (Android Package) adalah **installation package aplikasi Android**, proses loadingnya melibatkan beberapa langkah kunci:

1️⃣ **Parsing APK**: Parse `AndroidManifest.xml` untuk menentukan permission, informasi component.  
2️⃣ **Optimasi DEX**: Konversi `classes.dex` menjadi file OAT/ART, meningkatkan efisiensi runtime.  
3️⃣ **Class Loading**: Gunakan mekanisme `ClassLoader` untuk load Java class.  
4️⃣ **Resource Management**: Parse `resources.arsc` untuk load UI resources.  
5️⃣ **Loading Native Code**: Load library lokal `.so` di direktori `lib/`.  
6️⃣ **Eksekusi `Application.onCreate()`**, start aplikasi.

---

# **2️⃣ Parsing APK**
### **✅ 1. Parsing AndroidManifest.xml**
📌 **Lihat `AndroidManifest.xml` APK yang Terinstall**
```bash
adb shell dumpsys package com.example.app | grep android.intent.action.MAIN
```
📌 **Decompile APK untuk Lihat `AndroidManifest.xml`**
```bash
apktool d app.apk -o output/
cat output/AndroidManifest.xml
```
📌 **Modifikasi `AndroidManifest.xml` dan Repackage Ulang**
```bash
apktool b output -o modded.apk
jarsigner -verbose -keystore my.keystore modded.apk alias_name
adb install modded.apk
```

---

# **3️⃣ Loading DEX dan Optimasi OAT**
### **✅ 1. Loading DEX**
Android menggunakan Dalvik/ART runtime untuk load `classes.dex`, mengeksekusi Java code.

📌 **Lihat File DEX dalam APK**
```bash
unzip -l app.apk | grep classes.dex
```
📌 **Decompile DEX**
```bash
jadx -d output/ app.apk
```

### **✅ 2. Precompile ART OAT**
📌 **Perangkat Android 5.0+ konversi DEX menjadi file OAT**
```bash
adb shell ls /data/dalvik-cache/
```
📌 **Decompile OAT**
```bash
oatdump --oat-file=/data/dalvik-cache/arm64/system@framework@boot.art
```

---

# **4️⃣ Mekanisme Class Loading**
Android menggunakan **class loader (ClassLoader)** untuk load kode DEX:
| **Class Loader** | **Fungsi** | **Contoh** |
|------------|------|------|
| **PathClassLoader** | Load file DEX bawaan aplikasi | `system/lib/` |
| **DexClassLoader** | Load DEX dinamis saat runtime | `data/data/com.example.app/dex/` |

📌 **Contoh: Dynamic Loading DEX**
```java
DexClassLoader loader = new DexClassLoader("/sdcard/test.dex", "/data/data/com.example.app/dex/",
        null, getClassLoader());
Class<?> clazz = loader.loadClass("com.example.MyClass");
Method method = clazz.getMethod("test");
method.invoke(null);
```
📌 **Lihat ClassLoader**
```bash
adb shell dumpsys package com.example.app | grep class
```

---

# **5️⃣ Reverse Analysis Loading APK**
## **✅ 1. Dump File DEX**
**Gunakan Frida Ekstrak DEX yang Di-load Runtime**
```js
Java.perform(function() {
    var DexClassLoader = Java.use("dalvik.system.DexClassLoader");
    DexClassLoader.loadClass.implementation = function(name) {
        console.log("Loading class: " + name);
        return this.loadClass(name);
    };
});
```
Eksekusi:
```bash
frida -U -n com.example.app -e "..."
```

---

## **✅ 2. Hook `loadClass`**
**Intercept `PathClassLoader` Loading Class**
```js
Java.perform(function() {
    var PathClassLoader = Java.use("dalvik.system.PathClassLoader");
    PathClassLoader.loadClass.overload('java.lang.String').implementation = function(name) {
        console.log("Hooked class loading: " + name);
        return this.loadClass(name);
    };
});
```

---

## **✅ 3. Parse APK yang Terinstall**
📌 **List Semua Aplikasi yang Terinstall**
```bash
adb shell pm list packages -f
```
📌 **Ekstrak APK**
```bash
adb shell pm path com.example.app
adb pull /data/app/com.example.app/base.apk .
```
📌 **Decompile**
```bash
apktool d base.apk -o output/
```

---

## **✅ 4. Hook `Application.onCreate()`**
**Gunakan Xposed Hijack `Application.onCreate()`**
```java
findAndHookMethod("android.app.Application", "onCreate", new XC_MethodHook() {
    @Override
    protected void afterHookedMethod(MethodHookParam param) {
        Log.d("Xposed", "Application onCreate() called!");
    }
});
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Parse `AndroidManifest.xml`**
```bash
apktool d app.apk -o output/
cat output/AndroidManifest.xml
```
### **✅ 2. Dump DEX yang Di-load Runtime**
```js
Java.perform(function() {
    var DexClassLoader = Java.use("dalvik.system.DexClassLoader");
    DexClassLoader.loadClass.implementation = function(name) {
        console.log("Loading class: " + name);
        return this.loadClass(name);
    };
});
```
### **✅ 3. Hook `Application.onCreate()`**
```java
findAndHookMethod("android.app.Application", "onCreate", new XC_MethodHook() {
    @Override
    protected void afterHookedMethod(MethodHookParam param) {
        Log.d("Xposed", "Application onCreate() called!");
    }
});
```
---

# **📚 Referensi**
📌 **Loading APK**
- Dokumentasi resmi: [https://developer.android.com/guide/components](https://developer.android.com/guide/components)  
- Parsing ClassLoader: [https://source.android.com/devices/tech/dalvik/dex-format](https://source.android.com/devices/tech/dalvik/dex-format)  

📌 **Reverse Analysis**
- Frida Hook Android: [https://frida.re](https://frida.re)  
- Tutorial Xposed Hook: [https://github.com/rovo89/XposedBridge](https://github.com/rovo89/XposedBridge)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Mekanisme loading APK dari install hingga runtime**  
✅ **Proses optimasi DEX/OAT dan teknologi Hook**  
✅ **Cara menganalisis, memodifikasi APK untuk reverse debugging**  

🚀 **Langkah Selanjutnya (Day 15)**: **Menulis Kode ARM Assembly (Eksperimen)!** 🎯
