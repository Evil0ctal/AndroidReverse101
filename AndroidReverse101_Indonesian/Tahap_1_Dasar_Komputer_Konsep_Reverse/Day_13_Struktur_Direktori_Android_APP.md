# **📜 Day 13: Parsing Struktur Direktori Android APP**

## **📌 Tujuan Pembelajaran**
✅ **Memahami struktur direktori Android APP**, menguasai file penting di direktori `/data/data`.  
✅ **Mempelajari cara penyimpanan Android APP**, termasuk **SharedPreferences, SQLite, internal & external storage**.  
✅ **Menguasai distribusi file APK setelah install**, mempelajari **metode backup data aplikasi, migrasi, reverse analysis**.  
✅ **Menganalisis pengaruh `AndroidManifest.xml` terhadap path penyimpanan file**.  
✅ **Praktik ekstraksi data Android APP, melakukan reverse analysis & debugging**.  

---

# **1️⃣ Struktur Direktori Android APP**
Setelah Android APP diinstall, datanya terutama disimpan di direktori `/data/data/<package_name>/` (perlu akses Root).

📌 **Lihat Data Directory Semua APP yang Terinstall**
```bash
adb shell ls /data/data/
```

📌 **Contoh: Struktur Direktori Sebuah APP**
```
/data/data/com.example.app/
├── cache/                  # File cache (data temporary)
├── code_cache/             # ART compilation cache
├── databases/              # Direktori penyimpanan SQLite database
│   ├── user.db             # File database utama
│   ├── user.db-shm         # SQLite shared memory file
│   ├── user.db-wal         # SQLite WAL log file
├── files/                  # File yang disimpan aplikasi (default directory)
│   ├── logs/               # Log files
│   ├── config.json         # Configuration file
├── shared_prefs/           # SharedPreferences (XML format storage)
│   ├── settings.xml        # APP settings storage
│   ├── user_data.xml       # User data
├── lib/                    # Native C/C++ so libraries
│   ├── libnative.so        # Local shared library
├── app_webview/            # WebView storage data
├── app_flutter/            # Flutter APP resources
└── cache/                  # Temporary cache files
```

📌 **Lihat Direktori APP Tertentu**
```bash
adb shell ls -l /data/data/com.example.app/
```

---

# **2️⃣ Parsing Fungsi Direktori**
| **Direktori** | **Fungsi** | **Cara Penyimpanan** | **Apakah Dapat Diakses** |
|---------|---------|-------------|-------------|
| **`cache/`** | Temporary cache data | File | ✅ Bisa read/write (dalam app) |
| **`code_cache/`** | ART compilation cache | OAT file | 🚫 Hanya Root access |
| **`databases/`** | SQLite database APP | SQLite3 | 🚫 Hanya Root access |
| **`files/`** | File yang disimpan APP | Normal file | ✅ Bisa read/write (dalam app) |
| **`shared_prefs/`** | Shared storage, simpan app settings | XML | 🚫 Hanya Root access |
| **`lib/`** | Simpan Native .so libraries | ELF file | 🚫 Hanya Root access |
| **`app_webview/`** | WebView local storage | Cache | 🚫 Hanya Root access |
| **`cache/`** | Temporary cache | Cache | ✅ Bisa read/write (dalam app) |

📌 **Lihat Database File**
```bash
adb shell ls /data/data/com.example.app/databases/
```

📌 **Lihat SharedPreferences**
```bash
adb shell cat /data/data/com.example.app/shared_prefs/settings.xml
```

---

# **3️⃣ Cara Penyimpanan Android APP**
### **✅ 1. Internal Storage**
**APP private storage, APP lain tidak bisa akses (tanpa Root permission).**
```java
File file = new File(getFilesDir(), "config.json");
```
📌 **Lihat APP Internal Storage**
```bash
adb shell run-as com.example.app ls -l /data/data/com.example.app/files/
```

---

### **✅ 2. External Storage**
**Dapat diakses APP lain (perlu permission `WRITE_EXTERNAL_STORAGE`).**
```java
File file = new File(Environment.getExternalStorageDirectory(), "myfile.txt");
```
📌 **Lihat External Storage File**
```bash
adb shell ls -l /sdcard/Android/data/com.example.app/
```

---

### **✅ 3. SharedPreferences (Lightweight Storage)**
**Simpan data key-value pair sederhana (format XML).**
```java
SharedPreferences prefs = getSharedPreferences("settings", MODE_PRIVATE);
prefs.edit().putString("username", "admin").apply();
```
📌 **Lihat SharedPreferences**
```bash
adb shell cat /data/data/com.example.app/shared_prefs/settings.xml
```

---

### **✅ 4. SQLite Database**
**Simpan structured data**
```java
SQLiteDatabase db = openOrCreateDatabase("user.db", MODE_PRIVATE, null);
db.execSQL("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)");
```
📌 **Dump SQLite Database**
```bash
adb shell "sqlite3 /data/data/com.example.app/databases/user.db 'SELECT * FROM users;'"
```

---

# **4️⃣ Reverse Analysis Direktori APP**
## **✅ 1. Ekstrak APK Resources**
```bash
adb pull /data/app/com.example.app/base.apk .
apktool d base.apk -o output/
```
**Modifikasi `AndroidManifest.xml` untuk Lihat Permission Penyimpanan File**
```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
Repackage ulang:
```bash
apktool b output -o modded.apk
jarsigner -verbose -keystore my.keystore modded.apk alias_name
adb install modded.apk
```

---

## **✅ 2. Dapatkan Database Data**
📌 **Dump SQLite Database**
```bash
adb shell "sqlite3 /data/data/com.example.app/databases/user.db .dump"
```
📌 **Copy Database ke Local**
```bash
adb pull /data/data/com.example.app/databases/user.db .
sqlite3 user.db
```

---

## **✅ 3. Hook SharedPreferences**
📌 **Gunakan Frida Hook Baca SharedPreferences**
```js
Java.perform(function() {
    var SharedPreferences = Java.use("android.app.SharedPreferencesImpl");
    SharedPreferences.getString.implementation = function(key, defValue) {
        console.log("Hooked SharedPreferences: " + key);
        return this.getString(key, defValue);
    };
});
```
Eksekusi:
```bash
frida -U -n com.example.app -e "..."
```

---

## **✅ 4. Intercept File Access**
📌 **Gunakan Xposed Hook `open` Baca `config.json`**
```java
findAndHookMethod("java.io.FileInputStream", "open", String.class, new XC_MethodHook() {
    @Override
    protected void beforeHookedMethod(MethodHookParam param) {
        Log.d("Xposed", "File opened: " + param.args[0]);
    }
});
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Query Storage Path Aplikasi**
```bash
adb shell ls -l /data/data/com.example.app/
```
### **✅ 2. Ekstrak SQLite Data**
```bash
adb pull /data/data/com.example.app/databases/user.db .
sqlite3 user.db
```
### **✅ 3. Modifikasi SharedPreferences**
```bash
adb shell cat /data/data/com.example.app/shared_prefs/settings.xml
adb shell "echo '<string name=\"is_premium\">true</string>' >> /data/data/com.example.app/shared_prefs/settings.xml"
```
### **✅ 4. Hook File Access**
```js
Java.perform(function() {
    var File = Java.use("java.io.File");
    File.getAbsolutePath.implementation = function() {
        console.log("File Accessed: " + this.getAbsolutePath());
        return this.getAbsolutePath();
    };
});
```

---

# **📚 Referensi**
📌 **Mekanisme Penyimpanan Android**
- Panduan storage resmi: [https://developer.android.com/training/data-storage](https://developer.android.com/training/data-storage)  
- Penyimpanan database Android: [https://developer.android.com/training/data-storage/sqlite](https://developer.android.com/training/data-storage/sqlite)  

📌 **Reverse Engineering**
- Frida Hook file storage: [https://frida.re](https://frida.re)  
- SQLite reverse analysis: [https://github.com/sqlitebrowser/sqlitebrowser](https://github.com/sqlitebrowser/sqlitebrowser)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Struktur direktori Android APP, dan mekanisme penyimpanannya**  
✅ **Cara mendapatkan SQLite data, SharedPreferences**  
✅ **Cara menggunakan Frida/Xposed Hook file access**  

🚀 **Langkah Selanjutnya (Day 14)**: **Bagaimana APK Dimuat?** 🎯
