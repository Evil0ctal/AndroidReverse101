# **📜 Day 30: Reverse JNI dan Method Native**

## **📌 Tujuan Pembelajaran**
✅ Memahami JNI (Java Native Interface) mechanism  
✅ Menguasai cara reverse analysis Native methods  
✅ Mempelajari cara Hook JNI functions dengan Frida  
✅ Memahami mapping antara Java methods dan Native functions  
✅ Praktik: Reverse dan Hook Native library (.so)

---

# **1️⃣ Apa itu JNI?**
JNI memungkinkan Java code memanggil Native (C/C++) code.

**Java Side:**
```java
public class NativeLib {
    static {
        System.loadLibrary("native-lib");
    }
    public native String stringFromJNI();
}
```

**Native Side (C++):**
```cpp
extern "C" JNIEXPORT jstring JNICALL
Java_com_example_NativeLib_stringFromJNI(JNIEnv* env, jobject thiz) {
    return env->NewStringUTF("Hello from C++");
}
```

---

# **2️⃣ Find JNI Methods**
```bash
# List exports
nm -D libnative-lib.so | grep Java_

# Or use Frida
frida -U -n com.example.app -e 'Module.enumerateExports("libnative-lib.so")'
```

---

# **3️⃣ Hook JNI with Frida**
```javascript
Java.perform(function() {
    var moduleName = "libnative-lib.so";
    var nativeFunc = Module.findExportByName(moduleName, 
        "Java_com_example_NativeLib_stringFromJNI");
    
    Interceptor.attach(nativeFunc, {
        onEnter: function(args) {
            console.log("[*] JNI method called");
            console.log("    JNIEnv: " + args[0]);
            console.log("    jobject: " + args[1]);
        },
        onLeave: function(retval) {
            console.log("[*] Return value: " + retval);
            // Modify return
            var newStr = Java.vm.getEnv().newStringUtf("Hacked!");
            retval.replace(newStr);
        }
    });
});
```

---

# **4️⃣ Hook Native Crypto Functions**
```javascript
Interceptor.attach(Module.findExportByName("libnative-lib.so", "encrypt"), {
    onEnter: function(args) {
        console.log("Data: " + Memory.readUtf8String(args[0]));
        console.log("Key: " + Memory.readUtf8String(args[1]));
    },
    onLeave: function(retval) {
        console.log("Encrypted: " + retval);
    }
});
```

---

# **5️⃣ Bypass Native Anti-Debug**
```javascript
Interceptor.replace(Module.findExportByName("libc.so", "ptrace"), new NativeCallback(function() {
    console.log("[*] ptrace bypassed");
    return 0;
}, 'int', ['int', 'int', 'int', 'int']));
```

---

# **🛠 Tugas Praktik**
1. Find all JNI methods in an APK
2. Hook JNI string encryption function
3. Dump encryption keys from Native code
4. Bypass Native root detection

📚 **Referensi**: [JNI Spec](https://docs.oracle.com/javase/8/docs/technotes/guides/jni/)

🚀 **Langkah Selanjutnya (Day 31)**: **Pengenalan Xposed!** 🎯
