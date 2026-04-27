# Q221 — Find Hardcoded Password in APK Source

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb, apktool, jadx` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Decompile target_login.apk and search for hardcoded passwords in the Java source code. Check LoginActivity and Utils classes."

---

## 🎯 Flag Format

```
password=<value>
```

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Related tools: apktool, jadx, adb shell

**Hint 2**

`grep -rn password jadx_out/sources/`

---

## ✅ Solution

```bash
grep -rn password jadx_out/sources/
```

**Answer:** `password=P@ssw0rd_H4rdcoded`

---

## 🤖 Claude Setup Prompt

Ensure Android device connected and target APK available.

Report back: "Lab ready for Q221"
