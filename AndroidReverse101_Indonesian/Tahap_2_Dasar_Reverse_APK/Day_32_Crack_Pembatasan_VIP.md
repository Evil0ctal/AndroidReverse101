# **📜 Day 32: Crack Pembatasan VIP**

## **📌 Tujuan Pembelajaran**
✅ Memahami common VIP/Premium verification patterns  
✅ Menguasai teknik bypass VIP checks  
✅ Mempelajari cara identify verification logic  
✅ Memahami client-side vs server-side verification  
✅ Praktik: Unlock premium features tanpa bayar

---

# **1️⃣ Common VIP Patterns**

**Pattern 1: Boolean Field**
```java
public class User {
    private boolean isPremium = false;
    public boolean isPremium() {
        return this.isPremium;
    }
}
```

**Pattern 2: License Check**
```java
public boolean hasValidLicense() {
    String license = getLicense();
    return verifyLicense(license);
}
```

**Pattern 3: Server Verification**
```java
public void checkPremium() {
    API.getUserStatus(user, new Callback() {
        public void onSuccess(boolean isPremium) {
            // ...
        }
    });
}
```

---

# **2️⃣ Crack Method 1: Smali Modification**
```smali
# Find:
.method public isPremium()Z
    iget-boolean v0, p0, Lcom/example/User;->isPremium:Z
    return v0
.end method

# Change to:
.method public isPremium()Z
    const/4 v0, 0x1  # Always true
    return v0
.end method
```

---

# **3️⃣ Crack Method 2: Frida Hook**
```javascript
Java.perform(function() {
    var User = Java.use("com.example.User");
    User.isPremium.implementation = function() {
        return true;
    };
});
```

---

# **4️⃣ Crack Method 3: Xposed Module**
```java
findAndHookMethod("com.example.User", lpparam.classLoader,
    "isPremium",
    new XC_MethodReplacement() {
        @Override
        protected Object replaceHookedMethod(MethodHookParam param) {
            return true;
        }
    }
);
```

---

# **5️⃣ Bypass Server Checks**
```javascript
// Hook API response
Java.perform(function() {
    var API = Java.use("com.example.API");
    API.getUserStatus.implementation = function(user, callback) {
        // Call original
        this.getUserStatus(user, callback);
        // Force success callback
        callback.onSuccess(true);
    };
});
```

---

# **6️⃣ Complete Example**
```bash
# 1. Decompile
apktool d app.apk

# 2. Find VIP check
grep -r "isPremium\|isVIP\|hasPremium" smali/

# 3. Modify
vim smali/com/example/User.smali

# 4. Repackage & Sign
apktool b app -o cracked.apk
jarsigner -keystore my.keystore cracked.apk alias
adb install cracked.apk
```

---

# **🛠 Praktik**
1. Find VIP check in any app
2. Bypass using Smali modification
3. Bypass using Frida hook
4. Create Xposed module for permanent bypass

🚀 **Next (Day 33)**: **Bypass SSL Pinning!** 🎯
