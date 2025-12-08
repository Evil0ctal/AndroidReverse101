# **📜 Day 28: Menggunakan Frida Hook Method Java**

## **📌 Tujuan Pembelajaran**
✅ Menguasai Frida JavaScript API untuk Java method hooking  
✅ Mempelajari cara hook constructor, method, field  
✅ Memahami teknik modify arguments dan return values  
✅ Menguasai Frida script injection dan automation  
✅ Praktik: Hook berbagai Java methods untuk bypass logic

---

# **1️⃣ Setup Frida**
```bash
# Install Frida
pip install frida-tools

# Download frida-server untuk Android
wget https://github.com/frida/frida/releases/download/16.0.0/frida-server-16.0.0-android-arm64.xz
unxz frida-server-16.0.0-android-arm64.xz

# Push to device
adb push frida-server-16.0.0-android-arm64 /data/local/tmp/frida-server
adb shell chmod 755 /data/local/tmp/frida-server
adb shell /data/local/tmp/frida-server &
```

---

# **2️⃣ Hook Java Method**

## **Basic Hook**
```javascript
Java.perform(function() {
    var MainActivity = Java.use("com.example.MainActivity");
    
    MainActivity.checkPassword.implementation = function(password) {
        console.log("Password: " + password);
        return true;  // Always return true
    };
});
```

## **Hook Overloaded Methods**
```javascript
Java.perform(function() {
    var Utils = Java.use("com.example.Utils");
    
    // Hook specific overload
    Utils.encrypt.overload('java.lang.String').implementation = function(data) {
        console.log("Encrypting: " + data);
        return this.encrypt(data);
    };
});
```

## **Hook Constructor**
```javascript
Java.perform(function() {
    var User = Java.use("com.example.User");
    
    User.$init.implementation = function(name, isPremium) {
        console.log("Creating user: " + name);
        this.$init(name, true);  // Force premium
    };
});
```

---

# **3️⃣ Modify Arguments & Return Values**
```javascript
Java.perform(function() {
    var License = Java.use("com.example.License");
    
    License.verify.implementation = function(key) {
        console.log("Original key: " + key);
        var result = this.verify("VALID-KEY-1234");  // Use valid key
        console.log("Result: " + result);
        return result;
    };
});
```

---

# **4️⃣ Hook Static & Private Methods**
```javascript
Java.perform(function() {
    var Crypto = Java.use("com.example.Crypto");
    
    // Hook static method
    Crypto.hashPassword.implementation = function(password) {
        console.log("Hashing: " + password);
        return this.hashPassword(password);
    };
});
```

---

# **5️⃣ Save Frida Script**
```javascript
// script.js
Java.perform(function() {
    console.log("[*] Script loaded");
    
    var MainActivity = Java.use("com.example.Main Activity");
    MainActivity.isVIP.implementation = function() {
        return true;
    };
});
```

```bash
# Run script
frida -U -f com.example.app -l script.js
```

---

# **🛠 Tugas Praktik**
1. Hook login method untuk bypass authentication
2. Hook isVIP method untuk unlock premium
3. Hook hasLicense untuk bypass license check
4. Log semua password yang diinput user

---

📚 **Referensi**: [Frida Java API](https://frida.re/docs/javascript-api/#java)

🚀 **Langkah Selanjutnya (Day 29)**: **Praktik Frida Hook!** 🎯
