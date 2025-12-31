# **📜 Day 35: Reverse Algoritma Enkripsi (MD5, AES, RSA)**

## **📌 Tujuan Pembelajaran**
✅ Mengidentifikasi crypto algorithms dalam Android apps  
✅ Menguasai teknik extract encryption keys  
✅ Mempelajari cara decrypt encrypted data  
✅ Memahami common crypto vulnerabilities  
✅ Praktik: Reverse & crack crypto implementations

---

# **1️⃣ Common Crypto Algorithms**
- **Hashing**: MD5, SHA-1, SHA-256
- **Symmetric**: AES, DES, 3DES
- **Asymmetric**: RSA, ECC

---

# **2️⃣ Identify Crypto Usage**
```bash
# Search for crypto APIs
grep -r "Cipher\|MessageDigest\|KeyPair" smali/
```

---

# **3️⃣ Hook Crypto dengan Frida**
```javascript
Java.perform(function() {
    // Hook AES
    var Cipher = Java.use("javax.crypto.Cipher");
    Cipher.doFinal.overload('[B').implementation = function(data) {
        console.log("Plaintext: " + Java.use("java.lang.String").$new(data));
        console.log("Key: " + this.getIV());
        return this.doFinal(data);
    };
    
    // Hook MD5
    var MessageDigest = Java.use("java.security.MessageDigest");
    MessageDigest.digest.overload('[B').implementation = function(input) {
        console.log("MD5 input: " + Java.use("java.lang.String").$new(input));
        return this.digest(input);
    };
});
```

---

# **4️⃣ Extract Keys**
```javascript
// Dump symmetric key
var SecretKeySpec = Java.use("javax.crypto.spec.SecretKeySpec");
SecretKeySpec.$init.overload('[B', 'java.lang.String').implementation = function(key, alg) {
    console.log("Algorithm: " + alg);
    console.log("Key (hex): " + bytesToHex(key));
    return this.$init(key, alg);
};

function bytesToHex(bytes) {
    var hex = [];
    for (var i = 0; i < bytes.length; i++) {
        hex.push(('0' + (bytes[i] & 0xFF).toString(16)).slice(-2));
    }
    return hex.join('');
}
```

---

# **🛠 Tugas Praktik**
1. Find crypto usage in APK
2. Hook encryption functions
3. Extract encryption keys
4. Decrypt protected data

📚 **Referensi**: [Java Crypto API](https://docs.oracle.com/javase/8/docs/api/javax/crypto/package-summary.html)

🚀 **Next (Day 36)**: **Analisis Request WebSocket/API!** 🎯
