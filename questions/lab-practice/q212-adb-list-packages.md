# Q212 — List Installed Packages on Android

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟢 Easy |
| **Tools** | `adb` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"List all third-party (non-system) packages installed on the Android device. How many are there? Is there a suspicious app with `ceh` or `hack` in its package name?"

---

## 🎯 Flag Format

```
total_3rd_party=<number>; suspicious_pkg=<package_name>
```

---

## 💡 Hints

**Hint 1**

`adb shell pm list packages -3` — third-party packages list.

**Hint 2**

`adb shell pm list packages -3 | grep -i ceh`

---

## ✅ Solution

```bash
adb shell pm list packages -3
adb shell pm list packages -3 | wc -l
adb shell pm list packages -3 | grep -i "ceh\|hack"
```

**Answer:** `total_3rd_party=5; suspicious_pkg=com.ceh.targetapp`

---

## 🤖 Claude Setup Prompt

1. Install a test APK: `adb install /tmp/com.ceh.targetapp.apk`
2. Ensure device connected.

Report back: "Lab ready for Q212 — Android with test packages installed"
