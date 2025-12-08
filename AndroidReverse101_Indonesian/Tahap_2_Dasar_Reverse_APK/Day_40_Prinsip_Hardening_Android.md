# **📜 Day 40: Prinsip Hardening Android**

## **📌 Tujuan Pembelajaran**
✅ Memahami Android app hardening techniques  
✅ Menguasai code obfuscation methods  
✅ Mempelajari anti-tampering mechanisms  
✅ Memahami root/debug detection implementation  
✅ Praktik: Implement app protection

---

# **1️⃣ Hardening Layers**
1. **Code Obfuscation** (ProGuard/R8)
2. **String Encryption**
3. **Anti-Debugging**
4. **Root Detection**
5. **Integrity Checks**
6. **Native Code Protection**
7. **Runtime Protection** (Packing)

---

# **2️⃣ Code Obfuscation**
```proguard
# proguard-rules.pro
-optimizationpasses 5
-dontusemixedcaseclassnames
-dontskipnonpubliclibraryclasses
-verbose

# Obfuscate
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic

# Keep important classes
-keep class com.example.important.** { *; }
```

---

# **3️⃣ String Encryption**
```java
public class StringEncryption {
    private static final byte[] KEY = {0x1, 0x2, 0x3...};
    
    public static String decrypt(byte[] encrypted) {
        // XOR decryption
        for (int i = 0; i < encrypted.length; i++) {
            encrypted[i] ^= KEY[i % KEY.length];
        }
        return new String(encrypted);
    }
    
    // Usage
    String apiKey = decrypt(new byte[]{0x45, 0x67...});
}
```

---

# **4️⃣ Anti-Debugging Implementation**
```java
public class AntiDebug {
    public static boolean isDebuggable() {
        return android.os.Debug.isDebuggerConnected();
    }
    
    public static void detectDebugger() {
        new Thread(() -> {
            while (true) {
                if (isDebuggable()) {
                    System.exit(0); // Kill app
                }
                try { Thread.sleep(1000); } 
                catch (Exception e) {}
            }
        }).start();
    }
}
```

---

# **5️⃣ Root Detection**
```java
public class RootDetection {
    public static boolean isRooted() {
        // Check su binary
        String[] paths = {
            "/system/app/Superuser.apk",
            "/sbin/su",
            "/system/bin/su",
            "/system/xbin/su"
        };
        
        for (String path : paths) {
            if (new File(path).exists()) {
                return true;
            }
        }
        
        // Check BUILD tags
        String buildTags = android.os.Build.TAGS;
        return buildTags != null && buildTags.contains("test-keys");
    }
}
```

---

# **6️⃣ Integrity Check**
```java
public class IntegrityCheck {
    public static boolean verifySignature(Context context) {
        try {
            PackageInfo packageInfo = context.getPackageManager()
                .getPackageInfo(context.getPackageName(), 
                    PackageManager.GET_SIGNATURES);
            
            Signature[] signatures = packageInfo.signatures;
            byte[] cert = signatures[0].toByteArray();
            
            MessageDigest md = MessageDigest.getInstance("SHA-256");
            byte[] hash = md.digest(cert);
            
            String expectedHash = "ABC123..."; // Your cert hash
            return Arrays.equals(hash, hexToBytes(expectedHash));
            
        } catch (Exception e) {
            return false;
        }
    }
}
```

---

# **7️⃣ Native Protection**
```cpp
// JNI anti-debug
#include <sys/ptrace.h>

JNIEXPORT void JNICALL
Java_com_example_Security_antiDebug(JNIEnv *env, jobject thiz) {
    // ptrace self to prevent debugging
    ptrace(PTRACE_TRACEME, 0, 0, 0);
}
```

---

# **8️⃣ Commercial Hardening**
- **360 Jiagu** (奇虎360加固)
- **Bangcle** (梆梆加固)
- **Tencent Legu** (腾讯乐固)
- **DexGuard** (ProGuard commercial)

---

# **🛠 Implementasi**
```java
public class AppProtection {
    public static void init(Context context) {
        // Check environment
        if (RootDetection.isRooted()) {
            showWarning("Rooted device detected");
        }
        
        if (AntiDebug.isDebuggable()) {
            System.exit(0);
        }
        
        if (!IntegrityCheck.verifySignature(context)) {
            showWarning("App modified");
        }
        
        // Start monitoring
        AntiDebug.detectDebugger();
    }
}
```

---

# **🛠 Tugas Praktik**
1. Implement ProGuard obfuscation
2. Add string encryption
3. Implement root detection
4. Add signature verification
5. Test protection effectiveness

📚 **Referensi**:  
- [ProGuard Manual](https://www.guardsquare.com/manual/home)
- [OWASP MASVS](https://github.com/OWASP/owasp-masvs)

🚀 **Next (Day 41)**: **Decrypt APK Hardening!** 🎯
