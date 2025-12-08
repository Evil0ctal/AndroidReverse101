# **📜 Day 33: Bypass SSL Pinning**

## **📌 Tujuan Pembelajaran**
✅ Memahami SSL Pinning dan cara kerjanya  
✅ Menguasai teknik bypass SSL Pinning  
✅ Mempelajari cara intercept HTTPS traffic  
✅ Memahami certificate pinning implementations  
✅ Praktik: Bypass SSL pada aplikasi Android

---

# **1️⃣ Apa itu SSL Pinning?**
SSL Pinning memvalidasi certificate server untuk prevent MITM attacks.

**Types:**
- Certificate Pinning
- Public Key Pinning

---

# **2️⃣ Bypass dengan Frida**
```javascript
// Universal SSL Pinning Bypass
Java.perform(function() {
    // OkHttp3
    var CertificatePinner = Java.use("okhttp3.CertificatePinner");
    CertificatePinner.check.overload('java.lang.String', 'java.util.List').implementation = function() {
        console.log("[+] SSL Pinning bypassed (OkHttp3)");
    };
    
    // TrustManager
    var X509TrustManager = Java.use("javax.net.ssl.X509TrustManager");
    var SSLContext = Java.use("javax.net.ssl.SSLContext");
    
    var TrustManager = Java.registerClass({
        name: 'com.sensepost.test.TrustManager',
        implements: [X509TrustManager],
        methods: {
            checkClientTrusted: function(chain, authType) {},
            checkServerTrusted: function(chain, authType) {},
            getAcceptedIssuers: function() { return []; }
        }
    });
    
    var TrustManagers = [TrustManager.$new()];
    var SSLContext_init = SSLContext.init.overload(
        '[L javax.net.ssl.KeyManager;',
        '[Ljavax.net.ssl.TrustManager;',
        'java.security.SecureRandom'
    );
    SSLContext_init.implementation = function(keyManager, trustManager, secureRandom) {
        SSLContext_init.call(this, keyManager, TrustManagers, secureRandom);
    };
});
```

---

# **3️⃣ Bypass dengan Objection**
```bash
# Install objection
pip3 install objection

# Run
objection -g com.example.app explore

# Disable SSL
android sslpinning disable
```

---

# **4️⃣ Bypass dengan Xposed**
```java
// JustTrustMe module
// Or create custom:
findAndHookMethod("okhttp3.CertificatePinner", 
    lpparam.classLoader, "check",
    String.class, List.class,
    XC_MethodReplacement.DO_NOTHING
);
```

---

# **5️⃣ Setup Proxy**
```bash
# Burp Suite
# 1. Start Burp
# 2. Install Burp CA cert on device
# 3. Set proxy on device
adb shell settings put global http_proxy <IP>:8080

# 4. Run Frida bypass
frida -U -f com.example.app -l ssl-bypass.js
```

---

# **🛠 Tugas Praktik**
1. Install Burp Suite CA certificate
2. Configure device proxy
3. Use Frida to bypass SSL pinning
4. Intercept and modify HTTPS requests

📚 **Referensi**:  
- [Frida SSL Bypass](https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/)
- [Objection](https://github.com/sensepost/objection)

🚀 **Next (Day 34)**: **Obfuscation & Deobfuscation!** 🎯
