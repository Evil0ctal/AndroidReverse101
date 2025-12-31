# **📜 Day 21: Parsing Struktur File APK**

## **📌 Tujuan Pembelajaran**
✅ **Memahami secara mendalam struktur file APK**, menguasai fungsi dan metode parsing setiap komponen.  
✅ **Mempelajari cara menggunakan `apktool`, `jadx`, `aapt` parse APK, melihat AndroidManifest.xml, file DEX, resource file, dll.**  
✅ **Menguasai teknik decompile dan repackage APK, mempelajari cara memodifikasi code, bypass security mechanism**.  
✅ **Menganalisis `AndroidManifest.xml` permission declaration, entry Activity, component structure**.  
✅ **Praktik: Membongkar APK secara manual, ekstrak `classes.dex`, decompile Java code, modifikasi resource file**.  

---

# **1️⃣ Apa itu File APK?**
APK (Android Package) adalah installation package aplikasi Android, **pada dasarnya adalah ZIP archive**, berisi semua **application code, resources, configuration files**.

📌 **Lihat Struktur File APK**
```bash
unzip -l app.apk
```
Contoh output:
```
Archive:  app.apk
  Length      Date    Time    Name
---------  ---------- -----   ----
   123456  2024-02-22 12:34   AndroidManifest.xml
   234567  2024-02-22 12:34   classes.dex
   345678  2024-02-22 12:34   lib/arm64-v8a/libnative.so
   456789  2024-02-22 12:34   res/
   567890  2024-02-22 12:34   META-INF/
```
---

# **2️⃣ Parsing Struktur Utama APK**
| **File** | **Fungsi** |
|---------|---------|
| **AndroidManifest.xml** | Core configuration file aplikasi (permissions, components, entry point) |
| **classes.dex** | Dalvik Executable file (bytecode aplikasi) |
| **lib/** | Menyimpan `*.so` shared libraries (Native code ditulis C/C++) |
| **res/** | Menyimpan gambar, layouts, strings dan resources lainnya |
| **META-INF/** | Menyimpan signature, certificate information |
| **assets/** | Menyimpan uncompiled resources, seperti fonts, databases, config files |
| **resources.arsc** | Menyimpan resource IDs, optimized resource files |

---

# **3️⃣ Parsing AndroidManifest.xml**
AndroidManifest.xml adalah **configuration file aplikasi Android**, berisi:
- **Application permissions**
- **Four major components (Activity, Service, BroadcastReceiver, ContentProvider)**
- **Entry Activity**
- **Signature information**

📌 **Gunakan `aapt` Lihat Manifest**
```bash
aapt dump badging app.apk
```
Contoh output:
```
package: name='com.example.app' versionCode='1' versionName='1.0'
sdkVersion:'29'
targetSdkVersion:'33'
launchable-activity: name='com.example.MainActivity'
uses-permission: name='android.permission.INTERNET'
```

📌 **Gunakan `apktool` Decompile Manifest**
```bash
apktool d app.apk -o output/
cat output/AndroidManifest.xml
```
Contoh:
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.app">
    <uses-permission android:name="android.permission.INTERNET"/>
    <application android:label="Example App">
        <activity android:name="com.example.MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>
</manifest>
```

---

# **4️⃣ Parsing DEX (Dalvik Executable)**
File DEX adalah **bytecode yang dieksekusi Android virtual machine (ART/Dalvik)**, menyimpan executable file Java code setelah di-compile.

📌 **Ekstrak DEX**
```bash
unzip app.apk classes.dex
```
📌 **Gunakan `jadx` Decompile DEX**
```bash
jadx -d output/ classes.dex
```
📌 **Lihat Struktur DEX**
```bash
dexdump classes.dex | less
```
📌 **Decompile Java Code**
```bash
jadx -d output/ app.apk
```
Buka `output/com/example/MainActivity.java`, dapat melihat:
```java
package com.example;

public class MainActivity {
    public void onCreate() {
        System.out.println("Hello, Android Reverse Engineering!");
    }
}
```

---

# **5️⃣ Parsing Native Shared Library (lib/*.so)**
Beberapa APK **mengandung kode C/C++**, disimpan di direktori `lib/`.

📌 **Lihat SO File**
```bash
file lib/arm64-v8a/libnative.so
```
Contoh output:
```
libnative.so: ELF 64-bit LSB shared object, ARM aarch64
```
📌 **Decompile SO**
```bash
strings lib/arm64-v8a/libnative.so | less
```
📌 **Hook SO**
```js
Java.perform(function() {
    var nativeFunc = Module.findExportByName("libnative.so", "native_func");
    Interceptor.attach(nativeFunc, {
        onEnter: function(args) {
            console.log("native_func called!");
        }
    });
});
```

---

# **6️⃣ Modifikasi APK dan Repackage**
## **✅ 1. Modifikasi Resource File**
📌 **Replace APP Icon**
```bash
cp new_icon.png output/res/drawable/ic_launcher.png
```
📌 **Modifikasi APP Name**
Edit `output/res/values/strings.xml`:
```xml
<string name="app_name">Hacked App</string>
```

## **✅ 2. Modifikasi Java Code**
📌 **Decompile dan Modifikasi `MainActivity.java`**
```java
public void onCreate() {
    System.out.println("Hacked App Launched!");
}
```

## **✅ 3. Repackage**
```bash
apktool b output -o modded.apk
```

## **✅ 4. Re-sign**
```bash
jarsigner -verbose -keystore my.keystore modded.apk alias_name
```

## **✅ 5. Install APK**
```bash
adb install modded.apk
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Ekstrak DEX**
```bash
unzip app.apk classes.dex
```
### **✅ 2. Decompile Java**
```bash
jadx -d output/ app.apk
```
### **✅ 3. Lihat AndroidManifest.xml**
```bash
apktool d app.apk -o output/
cat output/AndroidManifest.xml
```
### **✅ 4. Hook SO**
```js
Java.perform(function() {
    var nativeFunc = Module.findExportByName("libnative.so", "native_func");
    Interceptor.attach(nativeFunc, {
        onEnter: function(args) {
            console.log("native_func called!");
        }
    });
});
```
### **✅ 5. Modifikasi dan Repackage**
```bash
apktool b output -o modded.apk
```
### **✅ 6. Re-sign**
```bash
jarsigner -verbose -keystore my.keystore modded.apk alias_name
```
### **✅ 7. Install Modified APK**
```bash
adb install modded.apk
```

---

# **📚 Referensi**
📌 **Parsing APK**
- `APKTool`: [https://github.com/iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool)  
- `AAPT`: [https://developer.android.com/studio/command-line/aapt2](https://developer.android.com/studio/command-line/aapt2)  

📌 **Parsing DEX**
- `JADX`: [https://github.com/skylot/jadx](https://github.com/skylot/jadx)  
- Decompile kode `smali`: [https://github.com/JesusFreke/smali](https://github.com/JesusFreke/smali)  

📌 **Native Reverse**
- `Frida`: [https://frida.re](https://frida.re)  
- `Ghidra`: [https://ghidra-sre.org/](https://ghidra-sre.org/)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Cara parse file APK, menganalisis Android code, resources, Manifest**  
✅ **Cara modifikasi APK resources, Java code, dan repackage**  
✅ **Cara Hook Native SO file, memodifikasi program logic**  

🚀 **Langkah Selanjutnya (Day 22)**: **Cara Decompile File DEX!** 🎯
