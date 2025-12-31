# **📜 Day 29: Praktik Frida Hook**

## **📌 Tujuan Pembelajaran**
✅ Praktik real-world Frida hooking scenarios  
✅ Menguasai advanced hooking techniques  
✅ Mempelajari debugging dan troubleshooting Frida scripts  
✅ Memahami cara bypass anti-frida protections  
✅ Praktik: Multiple complete hooking exercises

---

# **Exercise 1: Bypass Login**
```javascript
Java.perform(function() {
    var Auth = Java.use("com.example.Auth");
    Auth.login.implementation = function(user, pass) {
        console.log("Login attempt: " + user + ":" + pass);
        return true; // Always success
    };
});
```

---

# **Exercise 2: Dump Encryption Keys**
```javascript
Java.perform(function() {
    var Crypto = Java.use("com.example.Crypto");
    Crypto.encrypt.implementation = function(data, key) {
        console.log("Key: " + key);
        console.log("Data: " + data);
        return this.encrypt(data, key);
    };
});
```

---

# **Exercise 3: Modify API Response**
```javascript
Java.perform(function() {
    var API = Java.use("com.example.API");
    API.getUser.implementation = function() {
        var user = this.getUser();
        // Modify user object
        user.isPremium.value = true;
        return user;
    };
});
```

---

# **Exercise 4: Log All Method Calls**
```javascript
Java.perform(function() {
    var MainActivity = Java.use("com.example.MainActivity");
    var methods = MainActivity.class.getDeclaredMethods();
    
    methods.forEach(function(method) {
        var methodName = method.getName();
        try {
            MainActivity[methodName].implementation = function() {
                console.log("[*] Called: " + methodName);
                return this[methodName].apply(this, arguments);
            };
        } catch(e) {}
    });
});
```

---

# **🛠 Tugas Praktik**
1. Bypass authentication mechanism
2. Dump all network requests/responses
3. Modify in-app purchase verification
4. Log all SQLite queries

📚 **Referensi**: [Frida Codeshare](https://codeshare.frida.re)

🚀 **Langkah Selanjutnya (Day 30)**: **Reverse JNI dan Method Native!** 🎯
