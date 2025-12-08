# **📜 Day 18: Cara Debug Native Layer**

## **📌 Tujuan Pembelajaran**
✅ **Menguasai metode debugging Android Native layer**, termasuk GDB, LLDB, Frida remote debugging.  
✅ **Mempelajari cara load, analisis, dan debug Native shared library (.so)**.  
✅ **Menguasai metode menggunakan GDB untuk Native breakpoint debugging**, tracking JNI call flow.  
✅ **Memahami penerapan Frida dalam Native Hook**, memodifikasi program behavior secara dinamis.  
✅ **Praktik: Menggunakan GDB + Frida Hook `libnative.so`, debugging Native method call**.  

---

# **1️⃣ Mengapa Perlu Debug Native Layer?**
Aplikasi Android biasanya mengandung Native code (ditulis C/C++), code ini ada dalam bentuk **shared library (.so)**.  
Native code biasanya digunakan untuk:
- **JNI (Java Native Interface) calls** (seperti audio/video processing, algorithm library).
- **Anti-debugging & Anti-Hook** (umum pada aplikasi hardened).
- **High performance computing (seperti encryption/decryption, operasi matematika)**.

---

# **2️⃣ Persiapan**
📌 **Dapatkan Process ID (PID) Aplikasi Target**
```bash
adb shell ps -A | grep com.example.app
```
Contoh output:
```
u0_a123    1234  ...  com.example.app
```
👉 **Ingat `1234` - ini process ID!**

📌 **Dapatkan Native Library Aplikasi**
```bash
adb shell ls /data/app/com.example.app/lib/arm64/
```
Contoh output:
```
libnative.so
```
📌 **Copy `libnative.so` ke Local**
```bash
adb pull /data/app/com.example.app/lib/arm64/libnative.so .
```

---

# **3️⃣ Menggunakan GDB untuk Native Debugging**
### **✅ 1. Install `gdbserver`**
```bash
adb shell "cp /system/bin/gdbserver /data/local/tmp/"
adb shell chmod +x /data/local/tmp/gdbserver
```

### **✅ 2. Attach `gdbserver` ke Target Process**
```bash
adb shell su -c "/data/local/tmp/gdbserver :12345 --attach 1234"
```
(`1234` adalah target process ID)

📌 **Konfirmasi `gdbserver` Listening**
```bash
adb shell netstat -tulnp | grep 12345
```

---

### **✅ 3. Start GDB di Local**
📌 **Copy GDB dari Android NDK ke Local**
```bash
export PATH=$PATH:/path/to/android-ndk/prebuilt/linux-x86_64/bin
```

📌 **Connect ke Remote `gdbserver`**
```bash
gdb-multiarch
```
Kemudian di GDB execute:
```gdb
target remote <device IP>:12345
```

📌 **Load Target Library**
```gdb
add-symbol-file libnative.so 0x0000000000001000
```

---

# **4️⃣ Set Breakpoint dan Debugging**
📌 **List Semua Symbol**
```gdb
info functions
```

📌 **Set Breakpoint di `native_func`**
```gdb
break native_func
```

📌 **Mulai Debugging**
```gdb
continue
```

📌 **Lihat Register**
```gdb
info registers
```

📌 **Single-step Execution**
```gdb
stepi
```

📌 **Print Memory**
```gdb
x/10xw $sp
```

---

# **5️⃣ Menggunakan Frida untuk Dynamic Debugging**
### **✅ 1. Hook Native Function**
📌 **Hook `native_func` dalam `libnative.so`**
```js
var lib = Module.findExportByName("libnative.so", "native_func");
Interceptor.attach(lib, {
    onEnter: function(args) {
        console.log("native_func called!");
        console.log("Arg1: " + args[0].toInt32());
    },
    onLeave: function(retval) {
        console.log("native_func returned: " + retval.toInt32());
    }
});
```

📌 **Eksekusi Frida**
```bash
frida -U -n com.example.app -e "..."
```

---

### **✅ 2. Modifikasi Return Value**
📌 **Ubah Return Value `native_func`**
```js
Interceptor.attach(lib, {
    onLeave: function(retval) {
        retval.replace(999);
    }
});
```

📌 **Eksekusi**
```bash
frida -U -n com.example.app -e "..."
```

---

# **6️⃣ Reverse JNI Call**
📌 **Lihat JNI Method Table**
```bash
readelf -s libnative.so | grep Java_
```
Contoh output:
```
00001234 FUNC GLOBAL DEFAULT Java_com_example_app_NativeLib_nativeMethod
```

📌 **Dynamic Hook JNI**
```js
var nativeMethod = Module.findExportByName("libnative.so", "Java_com_example_app_NativeLib_nativeMethod");
Interceptor.attach(nativeMethod, {
    onEnter: function(args) {
        console.log("JNI nativeMethod called!");
    }
});
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Start `gdbserver`**
```bash
adb shell su -c "/data/local/tmp/gdbserver :12345 --attach 1234"
```
### **✅ 2. Connect GDB**
```bash
gdb-multiarch
target remote <device IP>:12345
```
### **✅ 3. Hook `native_func`**
```js
Interceptor.attach(Module.findExportByName("libnative.so", "native_func"), {
    onEnter: function(args) {
        console.log("Called!");
    }
});
```
### **✅ 4. Modifikasi Return Value**
```js
Interceptor.attach(Module.findExportByName("libnative.so", "native_func"), {
    onLeave: function(retval) {
        retval.replace(999);
    }
});
```

---

# **📚 Referensi**
📌 **GDB Debugging**
- Dokumentasi resmi `GDB`: [https://sourceware.org/gdb/](https://sourceware.org/gdb/)  

📌 **Frida Dynamic Debugging**
- Dokumentasi resmi `Frida`: [https://frida.re](https://frida.re)  

📌 **Android Native Reverse**
- Debugging `Android NDK`: [https://developer.android.com/ndk](https://developer.android.com/ndk)  

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Cara menggunakan GDB debug Android Native code**  
✅ **Cara menggunakan Frida Hook Native layer function**  
✅ **Cara memodifikasi ELF runtime behavior**  

🚀 **Langkah Selanjutnya (Day 19)**: **Parsing Mekanisme Keamanan Android APP!** 🎯
