# Q223 — Verify APK Signing Certificate

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb, apktool, jadx` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Check the signing certificate of signed.apk. Report the certificate issuer, validity period, and SHA-256 fingerprint."

---

## 🎯 Flag Format

```
issuer=<name>; fingerprint=<sha256>
```

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Related tools: apktool, jadx, adb shell

**Hint 2**

`apksigner verify --print-certs signed.apk`

---

## ✅ Solution

```bash
apksigner verify --print-certs signed.apk
```

**Answer:** `issuer=CN=CEH Lab; fingerprint=<sha256_value>`

---

## 🤖 Claude Setup Prompt

Ensure Android device connected and target APK available.

Report back: "Lab ready for Q223"
