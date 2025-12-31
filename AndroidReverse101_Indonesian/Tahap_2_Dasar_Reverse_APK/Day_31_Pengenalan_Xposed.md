# **📜 Day 31: Pengenalan Xposed**

## **📌 Tujuan Pembelajaran**
✅ Memahami Xposed Framework dan cara kerjanya  
✅ Menguasai setup Xposed di Android device  
✅ Mempelajari cara membuat Xposed module  
✅ Memahami hooking dengan Xposed API  
✅ Praktik: Buat Xposed module untuk hook aplikasi

---

# **1️⃣ Apa itu Xposed?**
Xposed adalah framework yang memungkinkan modifikasi sistem Android dan aplikasi tanpa memodifikasi APK.

**Kelebihan:**
- Persistent hooks (tidak perlu attach seperti Frida)
- System-wide modifications
- Module ecosystem

---

# **2️⃣ Setup Xposed**
```bash
# Install Xposed Framework (Root required)
# 1. Download Xposed Installer
# 2. Install framework
# 3. Reboot device
```

**Versi Modern:** LSPosed (untuk Android 8+)

---

# **3️⃣ Create Xposed Module**

**build.gradle:**
```gradle
dependencies {
    compileOnly 'de.robv.android.xposed:api:82'
}
```

**AndroidManifest.xml:**
```xml
<application>
    <meta-data
        android:name="xposedmodule"
        android:value="true" />
    <meta-data
        android:name="xposeddescription"
        android:value="My Hook Module" />
    <meta-data
        android:name="xposedminversion"
        android:value="82" />
</application>
```

**Hook Class:**
```java
public class MainHook implements IXposedHookLoadPackage {
    @Override
    public void handleLoadPackage(LoadPackageParam lpparam) {
        if (!lpparam.packageName.equals("com.example.app"))
            return;
        
        findAndHookMethod("com.example.MainActivity",
            lpparam.classLoader, "isVIP",
            new XC_MethodHook() {
                @Override
                protected void beforeHookedMethod(MethodHookParam param) {
                    param.setResult(true); // Always VIP
                }
            }
        );
    }
}
```

---

# **4️⃣ Common Hook Patterns**

**Hook Method:**
```java
findAndHookMethod("com.example.Auth", lpparam.classLoader,
    "checkPassword", String.class,
    new XC_MethodHook() {
        @Override
        protected void afterHookedMethod(MethodHookParam param) {
            param.setResult(true);
        }
    }
);
```

**Hook Constructor:**
```java
findAndHookConstructor("com.example.User", lpparam.classLoader,
    String.class, boolean.class,
    new XC_MethodHook() {
        @Override
        protected void beforeHookedMethod(MethodHookParam param) {
            param.args[1] = true; // Force premium
        }
    }
);
```

---

# **🛠 Tugas Praktik**
1. Install Xposed/LSPosed
2. Create simple hook module
3. Hook isVIP method
4. Hook network requests

📚 **Referensi**: [Xposed](https://github.com/rovo89/XposedBridge)

🚀 **Langkah Selanjutnya (Day 32)**: **Crack Pembatasan VIP!** 🎯
