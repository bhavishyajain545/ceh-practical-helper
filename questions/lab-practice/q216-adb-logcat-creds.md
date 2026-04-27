# Q216 — Find Credentials in Logcat

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"An insecure app leaks credentials in Android logcat. Use `adb logcat` to find the leaked username and password. Filter by the app's tag `CEHApp`."

---

## 🎯 Flag Format

```
user=<username>; pass=<password>
```

---

## 💡 Hints

**Hint 1**

`adb logcat -s CEHApp` — specific tag filter.

**Hint 2**

`adb logcat | grep -i "password\|credential\|login"`

---

## ✅ Solution

```bash
adb logcat -d | grep -i "password\|credential" 
adb logcat -d -s CEHApp
```

**Answer:** `user=admin; pass=leaked_pass123`

---

## 🤖 Claude Setup Prompt

```bash
adb shell log -t CEHApp "Login attempt: user=admin, password=leaked_pass123"
```

Report back: "Lab ready for Q216 — credentials planted in logcat"
