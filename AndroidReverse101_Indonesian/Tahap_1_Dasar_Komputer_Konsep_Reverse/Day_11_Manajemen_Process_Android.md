# **📜 Day 11: Manajemen Process Android**

## **📌 Tujuan Pembelajaran**
✅ **Memahami cara kerja manajemen process Android**, termasuk **Zygote process, App process, System Server process**.  
✅ **Menguasai siklus hidup process Android**, mempelajari **strategi scheduling foreground, background, service, dan cached process**.  
✅ **Mempelajari mekanisme OOM (Out Of Memory) management Android**, memahami **prioritas process dan strategi killing**.  
✅ **Menganalisis informasi process di direktori /proc**, melalui command `ps`, `top`, `dumpsys activity` mendapatkan status process.  
✅ **Mempelajari cara Hook mekanisme manajemen process Android**, melakukan persistensi aplikasi & reverse debugging.

---

# **1️⃣ Gambaran Umum Manajemen Process Android**
Di Android, aplikasi **biasanya tidak langsung membuat process**, tetapi di-spawn oleh **Zygote**.  

| **Tipe Process** | **Fungsi** | **Contoh** |
|------------|------|------|
| **Zygote** | Bertanggung jawab Fork process lain | `/system/bin/app_process` |
| **System Server** | Mengelola system services | process `system_server` |
| **App Process** | Menjalankan aplikasi | `com.example.app` |
| **Native Process** | Menjalankan kode C/C++ | `surfaceflinger`, `mediaserver` |

---

# **2️⃣ Parsing Process Kunci**
## **✅ 1. Zygote Process**
**Zygote adalah titik awal process Android**, fungsi utamanya:
- **Preload class library & resources**, mengurangi waktu startup aplikasi.
- **Fork child process**, semua App process di-copy dari Zygote.

📌 **Lihat Zygote Process**
```bash
adb shell ps -A | grep zygote
```
Contoh output:
```
zygote64   1234  567   123456K fg    00000000 S zygote64
zygote     1235  567   123456K fg    00000000 S zygote
```

📌 **Bagaimana Zygote Fork Process?**
```java
public static void main(String[] argv) {
    ZygoteServer zygoteServer = new ZygoteServer();
    while (true) {
        ZygoteConnection connection = zygoteServer.acceptCommandPeer();
        connection.runOnce();
    }
}
```
📌 **Reverse Analysis Zygote**
```bash
strings /system/bin/app_process
```

---

## **✅ 2. System Server Process**
**System Server process** bertanggung jawab mengelola Android system services, seperti **AMS (ActivityManagerService)**, **PMS (PackageManagerService)**.

📌 **Lihat System Server Process**
```bash
adb shell ps -A | grep system_server
```
Output:
```
system    1356  567   456789K fg    00000000 S system_server
```

📌 **Kode Kunci System Server**
```java
public static void main(String[] args) {
    SystemServer server = new SystemServer();
    server.run();
}
```

📌 **Analisis AMS dalam System Server**
```bash
adb shell dumpsys activity
```

---

## **✅ 3. Application Process**
Application process biasanya di-**Fork oleh Zygote**, bertanggung jawab mengeksekusi kode aplikasi.

📌 **Lihat App Process yang Sedang Berjalan**
```bash
adb shell ps -A | grep com.example.app
```

📌 **Lihat Detail Process Aplikasi**
```bash
adb shell dumpsys meminfo com.example.app
```

📌 **Kill Application Process**
```bash
adb shell am force-stop com.example.app
```

---

# **3️⃣ Prioritas Process & Mekanisme OOM**
Android mengadopsi **mekanisme manajemen prioritas OOM (Out Of Memory)**, berdasarkan tingkat kepentingan process menentukan **apakah process akan di-kill**.

| **Prioritas** | **Tipe Process** | **Dapat Di-kill** |
|-----------|----------|-----------|
| **0 (Tertinggi)** | Foreground process (foreground Activity) | ❌ Tidak dapat di-kill |
| **1** | Visible process (background Activity) | ❌ Biasanya dipertahankan |
| **2** | Service process (background Service) | ✅ Di-kill saat low memory |
| **3** | Background process (invisible Activity) | ✅ Di-kill saat low memory |
| **4 (Terendah)** | Cached process (App yang lama tidak digunakan) | ✅ Prioritas di-kill |

📌 **Lihat OOM Level Process**
```bash
adb shell cat /proc/1234/oom_adj
```
Output:
```
0   # Foreground process, tidak akan di-kill
6   # Background process, mungkin di-kill saat low memory
15  # Cached process, prioritas di-kill
```

📌 **Adjust OOM Level Process**
```bash
adb shell echo -17 > /proc/1234/oom_adj
```
👉 **Dapat digunakan untuk melindungi process, mencegah di-kill oleh system (perlu Root)**.

---

# **4️⃣ Android Process Management API**
## **✅ 1. ActivityManager**
```java
ActivityManager am = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
List<ActivityManager.RunningAppProcessInfo> processes = am.getRunningAppProcesses();
for (ActivityManager.RunningAppProcessInfo process : processes) {
    Log.d("Process", "PID: " + process.pid + " Name: " + process.processName);
}
```

## **✅ 2. Monitor Process State**
```java
ProcessLifecycleOwner.get().getLifecycle().addObserver(new LifecycleObserver() {
    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    public void onAppBackgrounded() {
        Log.d("Process", "App moved to background!");
    }
});
```

## **✅ 3. Membuat Process Run Persistent**
```java
startForegroundService(new Intent(this, MyService.class));
```

---

# **5️⃣ Reverse Analysis & Hook Process**
## **✅ 1. Hook Android Process Scheduling**
```bash
frida -U -n system_server -e "Interceptor.attach(Module.findExportByName(null, 'fork'), { onEnter: function(args) { console.log('fork called'); }})"
```

## **✅ 2. Bypass Process Killing**
```bash
adb shell setprop persist.sys.background_process_limit 0
```

## **✅ 3. Batasi CPU Usage Process**
```bash
taskset -p 0x01 1234
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Cek Process di Perangkat Android**
```bash
adb shell ps -A
```
### **✅ 2. Parse Zygote Process**
```bash
adb shell ps -A | grep zygote
```
### **✅ 3. Dapatkan Informasi Memori App Process**
```bash
adb shell dumpsys meminfo com.example.app
```
### **✅ 4. Monitor Process State**
```java
ProcessLifecycleOwner.get().getLifecycle().addObserver(new LifecycleObserver() {
    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    public void onAppBackgrounded() {
        Log.d("Process", "App moved to background!");
    }
});
```

---

# **📚 Referensi**
📌 **Android Process Management**
- Process `Zygote`: [https://source.android.com/devices/tech/dalvik/zygote](https://source.android.com/devices/tech/dalvik/zygote)  
- Scheduling process `AMS`: [https://developer.android.com/guide/components/activities/process-lifecycle](https://developer.android.com/guide/components/activities/process-lifecycle)  

📌 **Android Reverse**
- `Frida`: [https://frida.re](https://frida.re)  
- Hook process `Android`: [https://github.com/lasting-yang/AndroidReverseStudy](https://github.com/lasting-yang/AndroidReverseStudy)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Mekanisme inti manajemen process Android**  
✅ **Cara view & debug process Android**  
✅ **Cara Hook manajemen process Android, melakukan persistensi dan reverse analysis**  

🚀 **Langkah Selanjutnya (Day 12)**: **Parsing Mekanisme Permission Android!** 🎯
