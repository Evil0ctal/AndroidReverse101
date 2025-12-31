# **📜 Day 25: Eksperimen Modifikasi Kode Smali**

## **📌 Tujuan Pembelajaran**
✅ Praktik modifikasi kode Smali untuk bypass logic  
✅ Menguasai teknik crack VIP, remove ads, bypass license  
✅ Mempelajari debugging dan troubleshooting Smali  
✅ Memahami cara repackage dan sign APK hasil modifikasi  
✅ Praktik: Complete exercise dari modify hingga install

---

# **1️⃣ Skenario Praktik**

## **Scenario 1: Bypass License Check**
```smali
# Original
.method public checkLicense()Z
    invoke-virtual {p0}, Lcom/example/LicenseManager;->verify()Z
    move-result v0
    return v0
.end method

# Modified - Always return true
.method public checkLicense()Z
    const/4 v0, 0x1
    return v0
.end method
```

## **Scenario 2: Remove Ads**
```smali
# Original
.method public showAd()V
    invoke-static {}, Lcom/google/ads/AdManager;->show()V
    return-void
.end method

# Modified - Do nothing
.method public showAd()V
    return-void
.end method
```

## **Scenario 3: Unlock Premium Features**
```smali
# Original
.method public isPremium()Z
    iget-boolean v0, p0, Lcom/example/User;->premium:Z
    return v0
.end method

# Modified
.method public isPremium()Z
    const/4 v0, 0x1
    return v0
.end method
```

---

# **2️⃣ Workflow Lengkap**
```bash
# 1. Decompile
apktool d original.apk -o decompiled/

# 2. Find target method
grep -r "isPremium" decompiled/smali/

# 3. Edit Smali
vim decompiled/smali/com/example/User.smali

# 4. Repackage
apktool b decompiled/ -o modified.apk

# 5. Sign
jarsigner -verbose -keystore my.keystore modified.apk alias

# 6. Install
adb install -r modified.apk
```

---

# **🛠 Tugas Praktik**
Pilih salah satu:
1. Bypass login check
2. Remove ads from app
3. Unlock premium features
4. Bypass trial period

---

📚 **Referensi**: [APKTool](https://github.com/iBotPeaches/Apktool)

🚀 **Langkah Selanjutnya (Day 26)**: **Repackage dan Signing APK!** 🎯
