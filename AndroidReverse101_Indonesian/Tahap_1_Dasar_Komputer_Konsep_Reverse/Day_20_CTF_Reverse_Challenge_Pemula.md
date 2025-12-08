# **📜 Day 20: CTF Reverse Challenge (Pemula)**

## **📌 Tujuan Pembelajaran**
✅ **Memahami konsep dasar CTF reverse engineering**, menguasai tipe soal CTF reverse umum (CrackMe, Reversing, PWN).  
✅ **Mempelajari cara menganalisis ELF executable file, Android APK, Native shared library (.so)**.  
✅ **Menguasai metode static analysis (IDA Pro, Ghidra, Radare2) dan dynamic debugging (GDB, Frida)**.  
✅ **Menguasai teknik reverse umum dalam CTF, seperti string encryption, anti-debugging, dynamic Hook**.  
✅ **Praktik: Menulis dan crack sendiri soal CTF reverse, menganalisis encryption algorithm dan mendapatkan flag!**  

---

# **1️⃣ Apa itu CTF Reverse Challenge?**
CTF (Capture The Flag) adalah kompetisi information security, dimana **reverse engineering (Reversing)** adalah salah satu tipe soal umum.  
CTF reverse terutama mencakup:
- **CrackMe**: Menganalisis authentication logic binary program, bypass password check, menemukan flag.
- **PWN (Exploit)**: Menganalisis buffer overflow, format string vulnerability, dll.
- **Android Reversing**: Menganalisis APK, DEX, JNI, Hook code, bypass verification atau ekstrak sensitive data.

Bab ini menyediakan beberapa **source code soal CrackMe**, user dapat compile sendiri **ELF / APK / SO** file,  
berlatih **reverse analysis, bypass password verification, decrypt flag**, dll, dan menyediakan **solusi**.

---

# **2️⃣ ELF CrackMe Reverse Challenge**
## **✅ CrackMe  1 - Simple String Check**
📌 **Source Code CrackMe**
```c
#include <stdio.h>
#include <string.h>

void check_password(char *input) {
    if (strcmp(input, "SuperSecret123") == 0) {
        printf("Correct! Flag is FLAG{ELF_REVERSE_101}\n");
    } else {
        printf("Wrong password!\n");
    }
}

int main() {
    char password[32];
    printf("Enter password: ");
    scanf("%s", password);
    check_password(password);
    return 0;
}
```
📌 **Compile ELF**
```bash
gcc -o crackme1 crackme1.c
```
📌 **Solusi**
1. **Static Analysis**:
   ```bash
   strings crackme1
   ```
   Mungkin output:
   ```
   Enter password:
   Wrong password!
   Correct! Flag is FLAG{ELF_REVERSE_101}
   ```
   Langsung dapatkan flag, tidak perlu run program.

2. **Dynamic Debugging**:
   ```bash
   gdb ./crackme1
   break check_password
   run
   ```
   Di `check_password` set breakpoint, modifikasi input parameter bypass password check.

📌 **Flag**
```
FLAG{ELF_REVERSE_101}
```

---

## **✅ CrackMe 2 - XOR Encryption**
📌 **Source Code CrackMe**
```c
#include <stdio.h>
#include <string.h>

void decrypt(char *input) {
    char key = 0x55;
    char flag[] = {0x12, 0x36, 0x71, 0x55, 0x47, 0x00}; // XOR encrypted FLAG

    for (int i = 0; i < strlen(flag); i++) {
        flag[i] ^= key;
    }
    
    if (strcmp(input, flag) == 0) {
        printf("Correct! Flag is %s\n", flag);
    } else {
        printf("Wrong password!\n");
    }
}

int main() {
    char password[32];
    printf("Enter password: ");
    scanf("%s", password);
    decrypt(password);
    return 0;
}
```
📌 **Compile ELF**
```bash
gcc -o crackme2 crackme2.c
```
📌 **Solusi**
1. **Static Analysis**:
   ```bash
   objdump -d crackme2 | less
   ```
   Temukan bagian `XOR` encryption, menemukan key `0x55`.

2. **Python Decryption**
   ```python
   key = 0x55
   cipher = [0x12, 0x36, 0x71, 0x55, 0x47]
   print("".join(chr(c ^ key) for c in cipher))
   ```
   Output:
   ```
   FLAG{XOR_BYPASS}
   ```

📌 **Flag**
```
FLAG{XOR_BYPASS}
```

---

# **3️⃣ Android Crack Me Reverse Challenge**
## **✅ Java CrackMe**
📌 **CrackMe Java Code**
```java
package com.example.crackme;

import android.app.Activity;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        EditText input = findViewById(R.id.password);
        Button checkBtn = findViewById(R.id.check_btn);

        checkBtn.setOnClickListener(v -> {
            String userInput = input.getText().toString();
            if (userInput.equals("SuperSecret123")) {
                Toast.makeText(this, "Correct! Flag is FLAG{ANDROID_REVERSE}", Toast.LENGTH_LONG).show();
            } else {
                Toast.makeText(this, "Wrong password!", Toast.LENGTH_LONG).show();
            }
        });
    }
}
```
📌 **Solusi**
1. **Decompile APK**
   ```bash
   apktool d crackme.apk -o output/
   ```
2. **Cari Password**
   ```bash
   grep -r "SuperSecret123" output/
   ```
   Temukan hardcoded password:
   ```
   "SuperSecret123"
   ```
3. **Input password tersebut dapatkan flag**

📌 **Flag**
```
FLAG{ANDROID_REVERSE}
```

---

## **✅ JNI CrackMe**
📌 **CrackMe JNI Code**
```c
#include <jni.h>
#include <string.h>

JNIEXPORT jstring JNICALL
Java_com_example_crackme_NativeLib_check(JNIEnv *env, jobject thiz, jstring input) {
    const char *user_input = (*env)->GetStringUTFChars(env, input, 0);
    if (strcmp(user_input, "SecretJNI") == 0) {
        return (*env)->NewStringUTF(env, "Correct! Flag is FLAG{JNI_BYPASS}");
    }
    return (*env)->NewStringUTF(env, "Wrong password!");
}
```
📌 **Solusi**
1. **Ekstrak SO**
   ```bash
   adb pull /data/data/com.example.crackme/lib/arm64/libnative.so .
   ```
2. **Decompile**
   ```bash
   strings libnative.so | grep FLAG
   ```
   Temukan:
   ```
   FLAG{JNI_BYPASS}
   ```

📌 **Flag**
```
FLAG{JNI_BYPASS}
```

---

# **🛠 Tugas Praktik**
### **✅ 1. Compile dan Crack ELF**
```bash
gcc -o crackme1 crackme1.c
gdb ./crackme1
```
### **✅ 2. Crack APK**
```bash
apktool d app-debug.apk -o output/
grep -r "SuperSecret123" output/
```
### **✅ 3. Hook JNI**
```js
Java.perform(function() {
    var nativeMethod = Module.findExportByName("libnative.so", "Java_com_example_crackme_NativeLib_check");
    Interceptor.attach(nativeMethod, {
        onEnter: function(args) {
            console.log("JNI check() called!");
        }
    });
});
```

---

🔥 **Setelah menyelesaikan tugas, Anda akan menguasai:**  
✅ **Cara menulis & reverse ELF / APK / SO**  
✅ **Cara bypass password verification, memodifikasi binary logic**  
✅ **Cara menggunakan Frida/GDB Hook function kunci**  

🚀 **Langkah Selanjutnya (Day 21)**: **Parsing Struktur File APK!** 🎯

---

**🎉 SELAMAT! Anda telah menyelesaikan TAHAP 1!**  
📚 **Tahap 1 Selesai**: Dasar Komputer & Konsep Reverse (20 hari)  
🚀 **Lanjut ke Tahap 2**: Dasar Reverse APK (21 hari)
