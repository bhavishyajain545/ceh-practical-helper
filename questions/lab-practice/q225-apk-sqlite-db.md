# Q225 — Extract SQLite Database from App

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb, apktool, jadx` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Pull the app's SQLite database from the Android device. Open it and find stored credentials."

---

## 🎯 Flag Format

```
user=<username>; pass=<password>
```

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Related tools: apktool, jadx, adb shell

**Hint 2**

`adb pull /data/data/com.ceh.target/databases/users.db . && sqlite3 users.db 'SELECT * FROM users;'`

---

## ✅ Solution

```bash
adb pull /data/data/com.ceh.target/databases/users.db . && sqlite3 users.db 'SELECT * FROM users;'
```

**Answer:** `user=admin; pass=db_password_123`

---

## 🤖 Claude Setup Prompt

Ensure Android device connected and target APK available.

Report back: "Lab ready for Q225"
