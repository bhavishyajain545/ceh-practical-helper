# Q224 — Extract ADB Backup

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb, apktool, jadx` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Create an ADB backup of the target app and extract it to find sensitive data stored in app data directory."

---

## 🎯 Flag Format

```
flag=<string>
```

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Related tools: apktool, jadx, adb shell

**Hint 2**

`adb backup -f backup.ab com.ceh.target && abe unpack backup.ab backup.tar && tar xf backup.tar`

---

## ✅ Solution

```bash
adb backup -f backup.ab com.ceh.target && abe unpack backup.ab backup.tar && tar xf backup.tar
```

**Answer:** `flag=BACKUP_DATA_EXPOSED`

---

## 🤖 Claude Setup Prompt

Ensure Android device connected and target APK available.

Report back: "Lab ready for Q224"
