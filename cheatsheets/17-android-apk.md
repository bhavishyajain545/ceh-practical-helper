# 📱 APK / ANDROID DEVICE DIYA HAI — Secrets Nikalo

---

## PATH A: ADB Device Connected Hai

### CASE 1: Connect & Basic Info
```bash
adb connect <IP>:5555
adb devices
adb shell getprop ro.product.model
adb shell getprop ro.build.version.release
```

### CASE 2: Files Dhundo & Pull Karo
```bash
adb shell ls /sdcard/
adb shell find / -name "flag*" 2>/dev/null
adb pull /sdcard/Download/secret.txt .
adb pull /data/local/tmp/flag.txt .
```

### CASE 3: App Data Nikalo
```bash
adb shell ls /data/data/<package>/
adb pull /data/data/<package>/databases/users.db .
adb pull /data/data/<package>/shared_prefs/ ./prefs/
sqlite3 users.db "SELECT * FROM users;"
cat prefs/user_prefs.xml
```

### CASE 4: Logcat Mein Creds Leak
```bash
adb logcat -d | grep -i "password\|credential\|token\|key\|secret"
adb logcat -d -s AppTag
```

### CASE 5: Installed App Ka APK Pull Karo
```bash
adb shell pm list packages -3              # third-party apps
adb shell pm path com.target.app
adb pull /data/app/com.target.app/base.apk
```

---

## PATH B: APK File Di Hai

### CASE 1: Decompile — apktool (Smali + Resources)
```bash
apktool d target.apk -o decompiled/
cat decompiled/AndroidManifest.xml
cat decompiled/res/values/strings.xml
grep -ri "password\|api_key\|secret\|flag" decompiled/
```

### CASE 2: Decompile — jadx (Java Source Code)
```bash
jadx -d jadx_output/ target.apk
grep -rn "password\|apiKey\|SECRET\|token\|flag" jadx_output/sources/
```

### CASE 3: Hardcoded Credentials Dhundo
```bash
# strings.xml mein
grep -i "password\|key\|secret\|token" decompiled/res/values/strings.xml

# Java source mein
grep -rn "password\|api_key\|secret" jadx_output/sources/

# BuildConfig mein
cat jadx_output/sources/com/*/BuildConfig.java
```

### CASE 4: Permissions Check (Dangerous?)
```bash
grep "uses-permission" decompiled/AndroidManifest.xml
# Look for: READ_SMS, CAMERA, RECORD_AUDIO, READ_CONTACTS, ACCESS_FINE_LOCATION
```

### CASE 5: Debuggable Check
```bash
grep "debuggable" decompiled/AndroidManifest.xml
# android:debuggable="true" = INSECURE
```

### CASE 6: Network Security Config
```bash
cat decompiled/res/xml/network_security_config.xml
# cleartextTrafficPermitted="true" = INSECURE (allows HTTP)
```

---

## QUICK DECISION:
```
Android question
  ├─ Device connected?
  │   ├─ adb shell → find flag files
  │   ├─ Pull databases → sqlite3 read
  │   ├─ Pull shared_prefs → XML mein creds
  │   ├─ logcat → leaked credentials
  │   └─ pm path → pull APK → decompile
  └─ APK file di?
      ├─ apktool d → AndroidManifest + strings.xml
      ├─ jadx → Java source → grep secrets
      ├─ Hardcoded API keys / passwords
      ├─ Dangerous permissions check
      └─ debuggable / cleartext traffic check
```
