# **📜 Day 41: Decrypt APK Hardening Pemula**

## **📌 Tujuan Pembelajaran**
✅ Memahami APK packing/hardening mechanisms  
✅ Menguasai teknik unpack hardened APKs  
✅ Mempelajari cara dump original DEX dari memory  
✅ Memahami common packing solutions  
✅ Praktik: Unpack & analyze protected APKs

---

# **1️⃣ Apa itu APK Hardening?**
Hardening/Packing melindungi APK dengan:
- Encrypt original DEX
- Add protection shell
- Runtime DEX decryption
- Anti-debugging protection

**Common Packers:**
- 360 Jiagu (奇虎360)
- Tencent Legu (腾讯乐固)
- Bangcle (梆梆加固)
- Qihoo (奇虎加固)

---

# **2️⃣ Identify Hardened APK**
```bash
# Check for multiple DEX files
unzip -l app.apk | grep classes
# Output: classes.dex, classes2.dex, classes3.dex, etc.

# Check for packer signature
strings classes.dex | grep -i "jiagu\|bangcle\|tencent\|qihoo"

# Analyze Manifest
apktool d app.apk
cat app/AndroidManifest.xml | grep -i "application name"
# Look for: com.stub.StubApp or similar
```

---

# **3️⃣ Unp acking Methods**

## **Method 1: Memory Dump (FART)**
```bash
# Use FART (Frida ART Trace)
git clone https://github.com/hanbinglengyue/FART.git
python3 fart.py com.example.app

# Output: dumped DEX files
```

## **Method 2: Frida DEX Dump**
```javascript
// dex-dump.js
Java.perform(function() {
    var DexFile = Java.use("dalvik.system.DexFile");
    var BaseDexClassLoader = Java.use("dalvik.system.BaseDexClassLoader");
    
    BaseDexClassLoader.findClass.implementation = function(name) {
        var result = this.findClass(name);
        
        // Dump DEX when loaded
        var dexFile = this.pathList.value.dexElements.value[0].dexFile.value;
        var dexPath = dexFile.mFileName.value;
        
        console.log("[+] DEX loaded: " + dexPath);
        // Dump to file...
        
        return result;
    };
});
```

```bash
frida -U -f com.example.app -l dex-dump.js --no-pause
```

## **Method 3: Using Unpacking Tools**
```bash
# BlackDex
# 1. Install BlackDex APK on device
# 2. Select target app
# 3. Click unpack
# Output: /sdcard/BlackDex/

# FRIDA-DEXDump
git clone https://github.com/hluwa/FRIDA-DEXDump.git
python3 main.py -U -f com.example.app
```

---

# **4️⃣ Analyze Unpacked DEX**
```bash
# After dumping
jadx -d output/ dumped.dex

# Or use dex2jar
d2j-dex2jar dumped.dex
jd-gui dumped-dex2jar.jar
```

---

# **5️⃣ Bypass Packer Protection**
```javascript
// Bypass 360 Jiagu
Java.perform(function() {
    var StubApp = Java.use("com.stub.StubApp");
    StubApp.attachBaseContext.implementation = function(context) {
        console.log("[+] StubApp.attachBaseContext called");
        this.attachBaseContext(context);
        
        // DEX now decrypted in memory
        // Dump it here
    };
});
```

---

# **6️⃣ Complete Unpacking Workflow**
```bash
# 1. Identify packing
unzip -l app.apk | grep classes
strings classes.dex | head -20

# 2. Install on rooted device
adb install app.apk

# 3. Dump DEX
frida -U -f com.example.app -l dump.js --no-pause

# 4. Rebuild APK
apktool d -f dumped.dex -o unpacked/
apktool b unpacked/ -o unpacked.apk

# 5. Sign
jarsigner -keystore my.keystore unpacked.apk alias

# 6. Verify
jadx -d final/ unpacked.apk
```

---

# **7️⃣ Common Issues**
- **Incomplete dump**: DEX loaded in multiple stages
- **Corrupted DEX**: Integrity checks failed
- **Anti-frida**: Packer detects Frida
- **Multi-DEX**: Multiple DEX files to dump

**Solutions:**
- Use multiple dumping methods
- Disable anti-frida detection
- Dump all DEX loads
- Reconstruct from memory

---

# **🛠 Tugas Praktik**
1. Identify hardened APK (find one online)
2. Use FART/BlackDex to unpack
3. Analyze dumped DEX
4. Rebuild clean APK
5. Compare original vs unpacked

---

📚 **Referensi**:
- [FART](https://github.com/hanbinglengyue/FART)
- [BlackDex](https://github.com/CodingGay/BlackDex)
- [FRIDA-DEXDump](https://github.com/hluwa/FRIDA-DEXDump)
- [Unpacking Guide](https://github.com/AndroidCrackersClub)

---

🎉 **TAHAP 2 SELESAI!** 🎉

Anda telah menguasai:
✅ APK structure & decompilation
✅ Smali programming
✅ Dynamic debugging (Frida/Xposed)
✅ VIP/License cracking
✅ SSL pinning bypass
✅ Crypto analysis
✅ Game hacking
✅ Anti-debugging bypass
✅ APK hardening & unpacking

🚀 **Lanjut ke TAHAP 3 untuk Advanced Topics & CTF!** 🎯
