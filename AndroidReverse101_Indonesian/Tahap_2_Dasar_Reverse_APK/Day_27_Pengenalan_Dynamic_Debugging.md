# **📜 Day 27: Pengenalan Dynamic Debugging**

## **📌 Tujuan Pembelajaran**
✅ Memahami perbedaan static vs dynamic analysis  
✅ Menguasai tools dynamic debugging (Frida, Xposed, GDB)  
✅ Mempelajari cara attach debugger ke running process  
✅ Memahami technique runtime code injection  
✅ Praktik: Debug aplikasi Android secara real-time

---

# **1️⃣ Static vs Dynamic Analysis**
| **Static** | **Dynamic** |
|-----------|------------|
| Analisis kode tanpa run | Analisis saat runtime |
| Decompile, read code | Hook, modify behavior |
| Tidak perlu device | Perlu device/emulator |

---

# **2️⃣ Tools Dynamic Debugging**

## **Frida**
```bash
frida -U -f com.example.app
```

## **Xposed**
- Install Xposed Framework
- Create Xposed module
- Hook Java methods

## **Android Studio Debugger**
```bash
adb forward tcp:5005 tcp:5005
```

---

# **3️⃣ Frida Basic Hook**
```javascript
Java.perform(function() {
    var MainActivity = Java.use("com.example.MainActivity");
    
    MainActivity.onCreate.implementation = function() {
        console.log("onCreate() called!");
        this.onCreate();
    };
});
```

---

# **🛠 Tugas Praktik**
```bash
# Install Frida
pip install frida-tools

# List apps
frida-ps -U

# Attach to app
frida -U -n com.example.app
```

📚 **Referensi**: [Frida](https://frida.re)

🚀 **Langkah Selanjutnya (Day 28**): **Menggunakan Frida Hook Method Java!** 🎯
