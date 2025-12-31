# **📜 Day 36: Analisis Request WebSocket/API**

## **📌 Tujuan Pembelajaran**
✅ Memahami network communication analysis  
✅ Menguasai HTTP/HTTPS request interception  
✅ Mempelajari WebSocket traffic analysis  
✅ Memahami API reverse engineering  
✅ Praktik: Intercept & modify network traffic

---

# **1️⃣ Setup Proxy**
```bash
# Burp Suite
adb shell settings put global http_proxy <IP>:8080

# mitmproxy
mitmdump -s script.py

# Charles Proxy
# Configure device proxy settings
```

---

# **2️⃣ Hook Network Requests**
```javascript
Java.perform(function() {
    // Hook OkHttp
    var OkHttpClient = Java.use("okhttp3.OkHttpClient");
    var Request = Java.use("okhttp3.Request");
    
    OkHttpClient.newCall.implementation = function(request) {
        console.log("URL: " + request.url().toString());
        console.log("Method: " + request.method());
        console.log("Headers: " + request.headers().toString());
        return this.newCall(request);
    };
});
```

---

# **3️⃣ WebSocket Interceptorception**
```javascript
Java.perform(function() {
    var WebSocketListener = Java.use("okhttp3.WebSocketListener");
    WebSocketListener.onMessage.overload('okhttp3.WebSocket', 'java.lang.String').implementation = function(ws, text) {
        console.log("WS Message: " + text);
        this.onMessage(ws, text);
    };
});
```

---

# **4️⃣ Modify Requests**
```javascript
// Change request body
Request.Builder.method.implementation = function(method, body) {
    var newBody = RequestBody.create(
        MediaType.parse("application/json"), 
        '{"modified": true}'
    );
    return this.method(method, newBody);
};
```

---

# **🛠 Tugas Praktik**
1. Setup Burp/mitmproxy
2. Intercept API calls
3. Analyze WebSocket messages
4. Modify & replay requests

📚 **Referensi**: [mitmproxy](https://mitmproxy.org/)

🚀 **Next (Day 37)**: **Praktik Crack Aplikasi!** 🎯
