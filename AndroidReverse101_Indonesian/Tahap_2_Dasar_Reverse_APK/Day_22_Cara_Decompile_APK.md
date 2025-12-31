# **📜 Day 22: Cara Decompile APK**

## **📌 Tujuan Pembelajaran**
✅ **Memahami konsep inti decompile Android APK**, menguasai berbagai decompile tools dan metode.  
✅ **Mempelajari cara ekstrak `classes.dex` dan konversi kembali ke Java code**, menguasai `jadx`, `baksmali`, `dex2jar`, dll.  
✅ **Menguasai decompile `AndroidManifest.xml`, ekstrak resource file, analisis Smali code**.  
✅ **Mempelajari teknik modifikasi setelah decompile APK, termasuk bypass encryption, modify logic, repackage**.  
✅ **Menguasai decompile analysis SO shared library (Native layer), menggunakan `IDA Pro` dan `Ghidra` untuk reverse engineering**.  
✅ **Praktik: Decompile APK, ekstrak Java code, analisis SO file, modifikasi logic, repackage dan run!**  

---

# **1️⃣ Apa itu Decompile APK?**
Decompile APK adalah mengkonversi compiled APK file **kembali menjadi readable code**, sehingga dapat:
- Ekstrak Java code (`classes.dex` → `.java`).
- Lihat resource files (layout XML, images, strings).
- Analisis `AndroidManifest.xml` component dan permission information.
- Reverse analysis SO shared library (ELF file), menemukan sensitive logic dan encryption algorithm.
- Modify APP logic, remove ads, crack VIP restrictions, dll.

---

# **2️⃣ Tiga Metode Decompile APK**
| **Metode** | **Skenario Penggunaan** | **Tool** |
|---------|----------|------|
| **Java Decompile** | Restore Java code | `jadx`, `dex2jar` |
| **Smali Decompile** | Modify Dalvik bytecode | `apktool`, `smali` |
| **Native Reverse** | Reverse analysis `.so` file | `IDA Pro`, `Ghidra` |

---

# **3️⃣ Ekstrak Kode APK**
## **✅ 1. Ekstrak APK**
📌 **Dapatkan Installed APK**
```bash
adb shell pm list packages | grep example
adb shell pm path com.example.app
adb pull /data/app/com.example.app-1/base.apk .
```

## **✅ 2. Ekstrak `classes.dex`**
```bash
unzip base.apk classes.dex
```

---

# **4️⃣ Decompile Java Code**
## **✅ 1. Menggunakan `jadx`**
📌 **Install `jadx`**
```bash
git clone https://github.com/skylot/jadx.git
cd jadx
./gradlew dist
```
📌 **Decompile DEX**
```bash
jadx -d output/ base.apk
```
📌 **Lihat Decompiled Code**
```bash
cat output/com/example/MainActivity.java
```
Contoh output:
```java
package com.example;

public class MainActivity {
    public void onCreate() {
        System.out.println("Hello, Reverse Engineering!");
    }
}
```

---

## **✅ 2. Menggunakan `dex2jar` + `JD-GUI`**
📌 **Install `dex2jar`**
```bash
wget https://github.com/pxb1988/dex2jar/releases/download/v2.0/dex-tools-2.0.zip
unzip dex-tools-2.0.zip
```
📌 **Konversi DEX ke JAR**
```bash
./d2j-dex2jar.sh classes.dex
```
📌 **Gunakan JD-GUI untuk Buka**
```bash
jd-gui classes-dex2jar.jar
```

---

# **5️⃣ Decompile Smali Code**
## **✅ 1. Menggunakan `apktool`**
📌 **Install `apktool`**
```bash
wget https://raw.githubusercontent.com/iBotPeaches/Apktool/master/scripts/linux/apktool
chmod +x apktool
```
📌 **Decompile APK**
```bash
apktool d base.apk -o output/
```
📌 **Lihat Smali Code**
```bash
cat output/smali/com/example/MainActivity.smali
```
Contoh:
```smali
.method public onCreate()V
    .locals 1
    const-string v0, "Hello, Reverse Engineering!"
    invoke-static {v0}, Ljava/lang/System;->println(Ljava/lang/String;)V
.end method
```

---

## **✅ 2. Modifikasi Smali Code**
📌 **Modifikasi `MainActivity.smali`**
```smali
.method public onCreate()V
    .locals 1
    const-string v0, "Hacked by Reverse Engineer!"
    invoke-static {v0}, Ljava/lang/System;->println(Ljava/lang/String;)V
.end method
```
📌 **Repackage**
```bash
apktool b output -o modded.apk
```
📌 **Re-sign**
```bash
jarsigner -verbose -keystore my.keystore modded.apk alias_name
```
📌 **Install APK**
```bash
adb install modded.apk
```

---

# **6️⃣ Parsing SO Shared Library (Native Layer)**
📌 **Ekstrak SO File**
```bash
adb pull /data/data/com.example.app/lib/arm64/libnative.so .
```
📌 **Lihat ELF Structure**
```bash
readelf -h libnative.so
```
📌 **Lihat Exported Functions**
```bash
nm -D libnative.so
```

---

## **✅ 1. Menggunakan IDA Pro untuk Analisis SO**
📌 **Langkah-langkah**
1. **Buka IDA Pro**
2. **Pilih `libnative.so` dan set architecture (ARM/ARM64)**
3. **Analisis code, cari key functions**
4. **Cari strings**
   - `Shift + F12` lihat semua strings
   - `X` lihat cross-references

📌 **Contoh**
```c
if (strcmp(input, "SecretKey") == 0) {
    return "FLAG{IDA_NATIVE_REVERSE}";
}
```
**Solusi**: Cari string `"SecretKey"`, temukan key verification logic, modifikasi binary atau Hook function tersebut.

---

## **✅ 2. Menggunakan Ghidra Decompile SO**
📌 **Langkah-langkah**
1. **Buka Ghidra**
2. **Buat project baru, import `libnative.so`**
3. **Pilih processor architecture (ARM/ARM64)**
4. **Gunakan `Decompiler` restore C code**
5. **Cari `strcmp()` calls, analysis logic**

📌 **Contoh**
```c
bool check_password(char* input) {
    return strcmp(input, "SuperSecretKey") == 0;
}
```
**Solusi**: Dapat menggunakan Frida modifikasi `strcmp()` return value, implementasikan bypass verification.

---

## **✅ 3. Hook SO Runtime Modify Code**
📌 **Hook `check_password()`**
```js
Java.perform(function() {
    var nativeFunc = Module.findExportByName("libnative.so", "check_password");
    Interceptor.attach(nativeFunc, {
        onEnter: function(args) {
            console.log("check_password called!");
            args[0] = ptr("HackedPassword");
        }
    });
});
```
📌 **Run Frida**
```bash
frida -U -n com.example.app -e "..."
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Ekstrak `classes.dex`**
```bash
unzip base.apk classes.dex
```
### **✅ 2. Decompile Java**
```bash
jadx -d output/ base.apk
```
### **✅ 3. Decompile Smali**
```bash
apktool d base.apk -o output/
cat output/smali/com/example/MainActivity.smali
```
### **✅ 4. Modifikasi VIP Method**
```smali
.method public isVIP()Z
    const/4 v0, 0x1
    return v0
.end method
```
### **✅ 5. Parse SO**
```bash
readelf -h libnative.so
nm -D libnative.so
```
### **✅ 6. Gunakan IDA Pro untuk Analisis**
- Buka `libnative.so`
- Cari `strcmp()` calls
- Modify return value

---

# **📚 Referensi**
📌 **APK Decompile**
- `IDA Pro`: [https://hex-rays.com/](https://hex-rays.com/)  
- `Ghidra`: [https://ghidra-sre.org/](https://ghidra-sre.org/)  
- `Frida`: [https://frida.re](https://frida.re)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Cara decompile Java & Smali code**  
✅ **Cara reverse analysis Native SO code**  
✅ **Cara menggunakan IDA Pro, Frida Hook, Ghidra untuk reverse debugging**  

🚀 **Langkah Selanjutnya (Day 23)**: **Parsing Struktur File DEX!** 🎯
