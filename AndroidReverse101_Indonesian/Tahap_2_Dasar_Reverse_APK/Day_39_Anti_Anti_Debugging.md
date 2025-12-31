# **📜 Day 39: Anti Anti-Debugging**

## **📌 Tujuan Pembelajaran**
✅ Memahami anti-debugging techniques  
✅ Menguasai debugger detection bypass  
✅ Mempelajari root/emulator detection bypass  
✅ Memahami integrity check bypass  
✅ Praktik: Defeat all anti-analysis protections

---

# **1️⃣ Common Anti-Debug Techniques**
- Debugger detection (Debug.isDebuggerConnected())
- Root detection (su binary check)
- Emulator detection (Build properties)
- Frida detection (process/port scanning)
- Integrity checks (signature verification)

---

# **2️⃣ Bypass Debugger Detection**
```javascript
Java.perform(function() {
    var Debug = Java.use("android.os.Debug");
    
    Debug.isDebuggerConnected.implementation = function() {
        console.log("[+] Debugger check bypassed");
        return false;
    };
    
    Debug.waitingForDebugger.implementation = function() {
        return false;
    };
});
```

---

# **3️⃣ Bypass Root Detection**
```javascript
Java.perform(function() {
    // File existence check
    var File = Java.use("java.io.File");
    File.exists.implementation = function() {
        var path = this.getAbsolutePath();
        if (path.indexOf("su") >= 0 || path.indexOf("magisk") >= 0) {
            console.log("[+] Root file hidden: " + path);
            return false;
        }
        return this.exists();
    };
    
    // Build.TAGS check
    var Build = Java.use("android.os.Build");
    Build.TAGS.value = "release-keys";
});
```

---

# **4️⃣ Bypass Emulator Detection**
```javascript
Java.perform(function() {
    var Build = Java.use("android.os.Build");
    
    Build.FINGERPRINT.value = "google/redfin/redfin:11/RQ3A.210805.001.A1/7474174:user/release-keys";
    Build.MODEL.value = "Pixel 5";
    Build.MANUFACTURER.value = "Google";
    Build.BRAND.value = "google";
    Build.DEVICE.value = "redfin";
    Build.PRODUCT.value = "redfin";
});
```

---

# **5️⃣ Bypass Frida Detection**
```javascript
// Rename frida-server
adb push frida-server /data/local/tmp/system_server
adb shell chmod 755 /data/local/tmp/system_server
adb shell /data/local/tmp/system_server &

// Hide Frida traces
Java.perform(function() {
    // Hook port scanning
    var Socket = Java.use("java.net.Socket");
    Socket.$init.overload('java.lang.String', 'int').implementation = function(host, port) {
        if (port == 27042 || port == 27043) {
            throw new Error("Connection refused");
        }
        return this.$init(host, port);
    };
});
```

---

# **6️⃣ Bypass Signature Verification**
```javascript
Java.perform(function() {
    var PackageManager = Java.use("android.app.ApplicationPackageManager");
    
    PackageManager.getPackageInfo.overload('java.lang.String', 'int').implementation = function(pkgName, flags) {
        var result = this.getPackageInfo(pkgName, flags);
        
        // Fake signature
        if (pkgName == "com.example.app") {
            console.log("[+] Signature check bypassed");
            // Return original signature
        }
        
        return result;
    };
});
```

---

# **7️⃣ Universal Anti-Debug Bypass**
```javascript
// Comprehensive bypass script
Java.perform(function() {
    console.log("[*] Loading anti-debug bypass...");
    
    // Debugger
    Java.use("android.os.Debug").isDebuggerConnected.implementation = () => false;
    
    // Root
    Java.use("java.io.File").exists.implementation = function() {
        var path = this.getAbsolutePath();
        return path.includes("su") ? false : this.exists();
    };
    
    // Emulator
    var Build = Java.use("android.os.Build");
    Build.FINGERPRINT.value = "google/redfin/redfin:11/RQ3A.210805.001.A1/7474174:user/release-keys";
    
    console.log("[+] Anti-debug bypass loaded!");
});
```

---

# **🛠 Tugas Praktik**
1. Identify anti-debug checks in app
2. Bypass debugger detection
3. Bypass root detection
4. Bypass emulator detection
5. Create universal bypass script

📚 **Referensi**:  
- [Frida Codeshare Anti-Debug](https://codeshare.frida.re/)
- [Android Anti-Emulation](https://github.com/strazzere/anti-emulator)

🚀 **Next (Day 40)**: **Prinsip Hardening Android!** 🎯
