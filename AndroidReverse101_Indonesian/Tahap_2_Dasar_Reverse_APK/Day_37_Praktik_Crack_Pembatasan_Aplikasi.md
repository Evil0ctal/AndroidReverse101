# **📜 Day 37: Praktik Crack Pembatasan Aplikasi**

## **📌 Tujuan Pembelajaran**
✅ Menguasai complete app cracking workflow  
✅ Mempelajari various restriction bypass techniques  
✅ Memahami trial period & license cracking  
✅ Menguasai in-app purchase bypass  
✅ Praktik: Real-world app cracking exercises

---

# **1️⃣ Common Restrictions**
- Trial periods (30 days, etc.)
- Feature locks (premium only)
- License verification
- In-app purchases
- Ad removement

---

# **2️⃣ Bypass Trial Period**
```smali
# Find trial check
.method public isTrialExpired()Z
    # Original logic
.end method

# Modify to:
.method public isTrialExpired()Z
    const/4 v0, 0x0  # Always false
    return v0
.end method
```

---

# **3️⃣ Bypass In-App Purchase**
```javascript
Java.perform(function() {
    var BillingClient = Java.use("com.android.billingclient.api.BillingClient");
    
    // Hook purchase verification
    BillingClient.queryPurchases.implementation = function(skuType) {
        console.log("[+] Bypassing purchase check");
        var result = this.queryPurchases(skuType);
        // Modify result to show purchased
        return fakePurchaseResult();
    };
});
```

---

# **4️⃣ Remove Ads**
```javascript
Java.perform(function() {
    var AdManager = Java.use("com.google.android.gms.ads.AdView");
    AdManager.loadAd.implementation = function(adRequest) {
        console.log("[+] Ad blocked");
        // Don't load ad
    };
});
```

---

# **5️⃣ Complete Workflow**
```bash
# 1. Analyze app
jadx -d output/ app.apk

# 2. Find restrictions
grep -r "trial\|premium\|purchase" output/

# 3. Modify Smali
apktool d app.apk
vim smali/com/example/LicenseCheck.smali

# 4. Repackage
apktool b app -o cracked.apk

# 5. Sign & Install
jarsigner -keystore my.keystore cracked.apk alias
adb install cracked.apk
```

---

# **🛠 Tugas Praktik**
Pilih aplikasi dan crack:
1. Trial period restriction
2. Premium features lock
3. In-app purchase verification
4. Advertisement display

📚 **Referensi**: [Lucky Patcher](https://www.luckypatchers.com/)

🚀 **Next (Day 38)**: **Dasar Game Cracking!** 🎯
