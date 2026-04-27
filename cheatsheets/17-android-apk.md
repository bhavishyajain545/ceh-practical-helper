# ANDROID DEVICE / APK FILE — Secrets Nikalo

---

## Pehle Samjho: Android Security Basics

**Android app kaise kaam karta hai?**
- Har app ek `.apk` file hoti hai (Android Package) — ye basically ek ZIP file hai
- APK ke andar: Java/Kotlin code, resources (images, strings), AndroidManifest.xml
- App ka data `/data/data/<package_name>/` mein store hota hai (databases, preferences)
- ADB = Android Debug Bridge — computer se Android device control karne ka tool

**Kya dhundna hai CEH exam mein?**
- Hardcoded passwords, API keys, secret tokens
- Insecure permissions (SMS read, camera access bina zaroorat ke)
- Debuggable app (security risk)
- Cleartext traffic allowed (HTTP over HTTPS)
- SQLite databases mein stored credentials
- SharedPreferences mein sensitive data
- Logcat mein leaked information

**Do paths hain:**
- **PATH A:** ADB se live device connected hai → device explore karo
- **PATH B:** APK file di hai → decompile karke analyze karo

---

## PATH A: ADB Device Connected Hai

### CASE 1: Device Se Connect Karo

```bash
# Device se connect karo (IP aur port diya hoga)
adb connect 192.168.1.100:5555
# Output: connected to 192.168.1.100:5555

# Verify — connected devices list
adb devices
# Output:
# List of devices attached
# 192.168.1.100:5555    device     ← "device" likha hai = connected!
# Agar "offline" ya "unauthorized" dikhe → problem hai

# Device basic info
adb shell getprop ro.product.model        # Phone model
adb shell getprop ro.build.version.release # Android version
adb shell getprop ro.product.brand         # Brand name
adb shell whoami                           # Current user (shell)
```

**Agar connect nahi ho raha?**
- Port galat ho sakta hai: 5555 sabse common hai
- Firewall block kar raha hai
- Device pe USB debugging enabled hona chahiye
- `adb kill-server && adb start-server` karke retry karo

---

### CASE 2: Files Dhundo Aur Pull Karo

```bash
# SD Card explore karo
adb shell ls /sdcard/
adb shell ls /sdcard/Download/
adb shell ls /sdcard/DCIM/
adb shell ls /sdcard/Documents/

# Flag/secret files dhundo
adb shell find /sdcard/ -name "flag*" 2>/dev/null
adb shell find /sdcard/ -name "*.txt" 2>/dev/null
adb shell find /sdcard/ -name "secret*" 2>/dev/null

# Common interesting locations
adb shell ls /data/local/tmp/          # Temp files (often used in CTFs)
adb shell ls /data/data/               # App data directories
adb shell ls /sdcard/Android/data/     # External app data

# File pull karo (device se computer pe laao)
adb pull /sdcard/Download/secret.txt ./
adb pull /data/local/tmp/flag.txt ./
# Pull hone ke baad local machine pe dekho: cat secret.txt
```

> **`/sdcard/` = internal storage** (confusing naam hai but ye SD card nahi hai).
> Most user files yahan hoti hain.

---

### CASE 3: App Data Nikalo (Databases, Preferences)

Apps apna data `/data/data/<package_name>/` mein store karti hain.

```bash
# Pehle package name dhundo
adb shell pm list packages -3
# -3 = sirf third-party apps (system apps nahi)
# Output: package:com.target.app
#         package:com.example.secretapp

# App ka data directory explore karo
adb shell ls /data/data/com.target.app/
# Andar milega: databases/, shared_prefs/, files/, cache/

# SQLite Database pull karo (yahan credentials hote hain!)
adb pull /data/data/com.target.app/databases/ ./databases/
# Local machine pe databases folder aa jayega

# Database read karo
sqlite3 ./databases/users.db
# SQLite shell open hoga:
.tables                              # Kaunsi tables hain?
SELECT * FROM users;                 # Saara data dekho
SELECT username, password FROM users;# Sirf creds
.quit                                # Exit

# SharedPreferences pull karo (XML files — settings/tokens)
adb pull /data/data/com.target.app/shared_prefs/ ./prefs/
cat ./prefs/*.xml
# Yahan tokens, session IDs, saved passwords mil sakte hain
```

**SharedPreferences mein kya milega?**
```xml
<map>
  <string name="username">admin</string>
  <string name="password">secret123</string>  <!-- YE DEKHO! -->
  <string name="auth_token">eyJhbGciOiJIUzI1...</string>
</map>
```

> **Root access chahiye** `/data/data/` access karne ke liye.
> Agar permission denied aaye → `adb root` try karo (emulators mein kaam karta hai).

---

### CASE 4: Logcat — Log Mein Leaked Information

Developers galti se sensitive data log kar dete hain. Logcat se wo padh sakte ho.

```bash
# Saare logs dump karo aur search karo
adb logcat -d | grep -i "password\|credential\|token\|key\|secret\|flag"
# -d = dump and exit (live stream nahi, snapshot)

# Specific app ke logs
adb logcat -d -s "AppTag"
# -s = specific tag filter

# Recent logs mein URLs dhundo
adb logcat -d | grep -i "http\|url\|api"

# Logcat clear karke fresh start karo
adb logcat -c          # Clear logs
# App use karo / action perform karo
adb logcat -d          # Ab fresh logs dekho
```

> **Bahut baar exam mein:** Developer ne `Log.d("DEBUG", "Password: " + password)` jaise code likha hota hai.
> Logcat mein seedha password print ho jaata hai!

---

### CASE 5: Installed App Ka APK Pull Karo

Device pe installed app ka APK nikal ke decompile kar sakte ho.

```bash
# Saare third-party apps list karo
adb shell pm list packages -3
# Output: package:com.target.app

# APK ka path dhundo
adb shell pm path com.target.app
# Output: package:/data/app/com.target.app-1/base.apk

# APK pull karo
adb pull /data/app/com.target.app-1/base.apk ./target.apk
# Ab ye APK decompile karo (PATH B dekho neeche)
```

---

## PATH B: APK File Di Hai — Decompile Aur Analyze

### CASE 1: apktool Se Decompile (Resources + Smali Code)

apktool APK ko decode karta hai — AndroidManifest, resources, aur smali code milta hai.

```bash
# Decompile karo
apktool d target.apk -o decompiled/
# d = decode
# -o = output directory

# Kya mila andar?
ls decompiled/
# AndroidManifest.xml  — app permissions, activities, services
# res/                 — resources (strings, layouts, images)
# smali/               — decompiled code (Smali format — low level)
# assets/              — extra files (configs, databases)

# AndroidManifest.xml padho (BAHUT IMPORTANT)
cat decompiled/AndroidManifest.xml

# strings.xml padho (hardcoded strings)
cat decompiled/res/values/strings.xml
# Yahan app ke saare text strings hote hain
# API keys, passwords, URLs — sab mil sakte hain

# Secrets dhundo (recursive search)
grep -ri "password\|api_key\|secret\|flag\|token\|key" decompiled/
# -r = recursive, -i = case insensitive
# Bahut saare results aa sakte hain — dhyan se dekho
```

---

### CASE 2: jadx Se Decompile (Java Source Code — Readable!)

jadx APK ko wapas Java code mein convert karta hai — padhna bahut easy hota hai.

```bash
# jadx se decompile karo
jadx -d jadx_output/ target.apk
# -d = output directory

# Java source code milega
ls jadx_output/sources/
# com/target/app/ — package structure

# Secrets dhundo Java code mein
grep -rn "password\|apiKey\|SECRET\|token\|flag\|API_KEY\|secret" jadx_output/sources/
# -n = line numbers bhi dikhao

# Specific classes dekho
find jadx_output/sources/ -name "*.java" | head -20
cat jadx_output/sources/com/target/app/MainActivity.java
```

**apktool vs jadx — kab kya use karein?**
- **apktool:** AndroidManifest, strings.xml, resources ke liye best
- **jadx:** Java source code padhne ke liye best (logic samajhna ho toh)
- **Dono chalao** — different cheezon ke liye dono useful hain

---

### CASE 3: Hardcoded Credentials/API Keys Dhundo

Ye sabse important hai — developers galti se code mein credentials daal dete hain.

```bash
# strings.xml mein (sabse common hiding spot)
grep -i "password\|key\|secret\|token\|api" decompiled/res/values/strings.xml
# Example output:
# <string name="api_key">AIzaSyD-FAKE-KEY-HERE</string>
# <string name="admin_password">P@ssw0rd!</string>

# BuildConfig.java mein (build-time constants)
find jadx_output/ -name "BuildConfig.java" -exec cat {} \;
# public static final String API_KEY = "sk-live-xxxx";

# Java source mein hardcoded strings
grep -rn "\"http\|\"https\|password\|secret\|admin" jadx_output/sources/
# URLs, credentials, debug strings — sab milega

# assets folder mein config files
ls decompiled/assets/
cat decompiled/assets/config.json 2>/dev/null
cat decompiled/assets/app.properties 2>/dev/null
# JSON/XML config files mein API endpoints aur keys hoti hain

# .so files mein (native libraries)
strings decompiled/lib/armeabi-v7a/*.so 2>/dev/null | grep -i "key\|secret\|password"
```

---

### CASE 4: Permissions Check — Dangerous Permissions Dhundo

AndroidManifest.xml mein app kaunsi permissions maangti hai — insecure permissions = security issue.

```bash
# Saari permissions list karo
grep "uses-permission" decompiled/AndroidManifest.xml
```

**Dangerous Permissions (Ye report karo agar milein):**

| Permission | Risk | Kyun Dangerous |
|---|---|---|
| `READ_SMS` / `SEND_SMS` | HIGH | SMS padh/bhej sakta hai (OTP steal) |
| `READ_CONTACTS` | HIGH | Contact list chura sakta hai |
| `CAMERA` | HIGH | Camera access bina bataye |
| `RECORD_AUDIO` | HIGH | Microphone sun sakta hai |
| `ACCESS_FINE_LOCATION` | HIGH | Exact GPS location track |
| `READ_CALL_LOG` | MEDIUM | Call history dekh sakta hai |
| `READ_EXTERNAL_STORAGE` | MEDIUM | Saari files padh sakta hai |
| `INTERNET` | LOW (but required) | Network access (data bhej sakta hai) |

> **CEH exam mein:** Agar question puchhe "which permissions are dangerous?" toh
> upar wali list se match karo. Calculator app mein CAMERA permission = suspicious!

---

### CASE 5: Debuggable Check (Security Vulnerability)

```bash
# AndroidManifest.xml mein debuggable check karo
grep -i "debuggable" decompiled/AndroidManifest.xml
# android:debuggable="true" = INSECURE!
# Production app mein debuggable true nahi hona chahiye

# Agar debuggable=true hai toh:
# - Attacker app ka data access kar sakta hai
# - Debug logs padh sakta hai
# - Code inject kar sakta hai
```

> **android:debuggable="true"** milna = big security finding.
> Report mein likhna: "App is set to debuggable mode which allows unauthorized access."

---

### CASE 6: Network Security Config (HTTP vs HTTPS)

```bash
# Network security config file dhundo
cat decompiled/res/xml/network_security_config.xml

# Kya dekhna hai:
# <base-config cleartextTrafficPermitted="true">  ← INSECURE!
# Ye matlab app HTTP (unencrypted) traffic allow karti hai
# Credentials plaintext mein jaayenge

# AndroidManifest mein bhi check karo
grep -i "cleartext\|usesCleartextTraffic\|networkSecurityConfig" decompiled/AndroidManifest.xml
# android:usesCleartextTraffic="true" = INSECURE
```

**Kya hai issue?**
- `cleartextTrafficPermitted="true"` → App HTTP use karti hai (no encryption)
- Man-in-the-middle attack ho sakta hai
- Passwords plaintext mein travel karte hain

---

### CASE 7: Certificate Pinning Check

```bash
# Network security config mein pinning check karo
cat decompiled/res/xml/network_security_config.xml
# <pin-set> tag dhundo — agar nahi hai toh pinning nahi hai

# Java code mein certificate pinning
grep -rn "CertificatePinner\|TrustManager\|X509\|ssl" jadx_output/sources/
# Agar custom TrustManager hai jo sab certificates accept karta hai = INSECURE
```

---

## Common Mistakes (Mat Karna Ye)

1. **ADB connect mein port bhool jaana** → Default 5555 hai
   - `adb connect IP:5555` — port zaroor likho

2. **`/data/data/` access karne pe permission denied** → Root chahiye
   - `adb root` try karo, ya `adb shell su` (rooted devices mein)

3. **apktool aur jadx mein confuse hona** → Dono different output dete hain
   - apktool = AndroidManifest + resources + smali
   - jadx = readable Java source code
   - **Dono chalao** — complete picture milegi

4. **Sirf ek jagah search karna** → Secrets kahin bhi ho sakte hain
   - strings.xml, Java code, BuildConfig, assets, SharedPreferences, databases — sab check karo

5. **grep mein `-r` bhool jaana** → Sirf ek file search hogi
   - `grep -ri "password" decompiled/` — `-r` se recursive search hogi

6. **SQLite database open nahi ho rahi** → sqlite3 installed nahi hai
   - `apt install sqlite3` se install karo

7. **Logcat bahut bada output** → Grep se filter karo
   - `adb logcat -d | grep -i password` — specific keywords dhundo

---

## Quick Decision Tree

```
Android question aaya — kya karu?
  │
  ├─ Device connected hai? (ADB)
  │   ├─ adb connect IP:5555
  │   ├─ adb devices (verify connection)
  │   ├─ Files dhundo:
  │   │   ├─ adb shell find /sdcard/ -name "flag*"
  │   │   └─ adb pull <path> ./ (download to local)
  │   ├─ App data nikalo:
  │   │   ├─ adb shell pm list packages -3 (app list)
  │   │   ├─ Pull databases/ → sqlite3 read
  │   │   └─ Pull shared_prefs/ → XML mein creds
  │   ├─ Logcat check karo:
  │   │   └─ adb logcat -d | grep -i "password\|token\|key"
  │   └─ App ka APK pull karo → decompile (PATH B)
  │
  └─ APK file di hai?
      ├─ apktool d target.apk -o decompiled/
      │   ├─ AndroidManifest.xml → permissions, debuggable
      │   ├─ res/values/strings.xml → hardcoded strings
      │   └─ res/xml/network_security_config.xml → cleartext check
      ├─ jadx -d jadx_output/ target.apk
      │   └─ Java source → grep secrets/passwords/keys
      ├─ grep -ri "password\|secret\|key\|flag" decompiled/
      ├─ Dangerous permissions? → READ_SMS, CAMERA, etc.
      ├─ debuggable="true"? → Security issue!
      └─ cleartextTrafficPermitted="true"? → Insecure!
```
