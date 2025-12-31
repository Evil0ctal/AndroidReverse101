# **📜 Day 38: Dasar Game Cracking**

## **📌 Tujuan Pembelajaran**
✅ Memahami Android game architecture  
✅ Menguasai memory editing techniques  
✅ Mempelajari speed hacks & value modification  
✅ Memahami anti-cheat bypass  
✅ Praktik: Modify game values & behavior

---

# **1️⃣ Game Hacking Tools**
- **GameGuardian**: Memory editor
- **Cheat Engine**: PC-based analysis
- **Frida**: Runtime manipulation
- **Xposed**: Permanent hooks

---

# **2️⃣ GameGuardian Basics**
```bash
# 1. Root device & install GG
# 2. Select game process
# 3. Search for value (e.g., coins: 1000)
# 4. Change value in game
# 5. Refine search (new value: 900)
# 6. Modify found address
# 7. Freeze value (lock)
```

---

# **3️⃣ Frida Game Hacks**
```javascript
Java.perform(function() {
    // Modify currency
    var Player = Java.use("com.game.Player");
    Player.getCoins.implementation = function() {
        return 999999;
    };
    
    // God mode
    Player.takeDamage.implementation = function(amount) {
        console.log("[+] Damage nullified");
        // Don't take damage
    };
    
    // Speed hack
    var GameSpeed = Java.use("com.game.GameEngine");
    GameSpeed.getSpeed.implementation = function() {
        return 5.0; // 5x speed
    };
});
```

---

# **4️⃣ Unity Game Hacking**
```bash
# Dump IL2CPP
il2cppdumper GameAssembly.so global-metadata.dat output/

# Find functions
grep "GetGold\|GetCoins" output/dump.cs

# Hook with Frida
Interceptor.attach(Module.findExportByName("libil2cpp.so", "Player_GetCoins"), {
    onLeave: function(retval) {
        retval.replace(999999);
    }
});
```

---

# **5️⃣ Bypass Anti-Cheat**
```javascript
Java.perform(function() {
    // Bypass root detection
    var RootCheck = Java.use("com.game.security.RootDetector");
    RootCheck.isRooted.implementation = function() {
        return false;
    };
    
    // Bypass memory check
    var SecurityCheck = Java.use("com.game.security.MemoryCheck");
    SecurityCheck.detectGameGuardian.implementation = function() {
        return false;
    };
});
```

---

# **🛠 Tugas Praktik**
1. Install GameGuardian
2. Modify game currency
3. Implement god mode
4. Create speed hack
5. Bypass anti-cheat

📚 **Referensi**:  
- [GameGuardian](https://gameguardian.net/)
- [IL2CPPDumper](https://github.com/Perfare/Il2CppDumper)

🚀 **Next (Day 39)**: **Anti Anti-Debugging!** 🎯
