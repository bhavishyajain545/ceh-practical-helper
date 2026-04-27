# Q222 — Analyze AndroidManifest.xml Permissions

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb, apktool, jadx` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Decompile insecure.apk and list all permissions in AndroidManifest.xml. Identify any dangerous permissions (CAMERA, READ_SMS, RECORD_AUDIO, etc.)."

---

## 🎯 Flag Format

```
dangerous_perms=<count>; worst=<permission>
```

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Related tools: apktool, jadx, adb shell

**Hint 2**

`grep 'uses-permission' decompiled/AndroidManifest.xml`

---

## ✅ Solution

```bash
grep 'uses-permission' decompiled/AndroidManifest.xml
```

**Answer:** `dangerous_perms=5; worst=android.permission.READ_SMS`

---

## 🤖 Claude Setup Prompt

Ensure Android device connected and target APK available.

Report back: "Lab ready for Q222"
