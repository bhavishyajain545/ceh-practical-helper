# Q215 — Get Shell Access on Android via ADB

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟢 Easy |
| **Tools** | `adb` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Get a shell on the Android device via ADB. Check the current user, list running processes, and find the flag file in `/data/local/tmp/`. Report the user and flag."

---

## 🎯 Flag Format

```
user=<username>; flag=<string>
```

---

## 💡 Hints

**Hint 1**

`adb shell` → `whoami` → `ls /data/local/tmp/`

**Hint 2**

If permission denied on /data, try `adb root` first.

---

## ✅ Solution

```bash
adb shell whoami
adb shell cat /data/local/tmp/flag.txt
```

**Answer:** `user=shell; flag=ADB_SHELL_ACCESS`

---

## 🤖 Claude Setup Prompt

```bash
adb shell "echo 'ADB_SHELL_ACCESS' > /data/local/tmp/flag.txt"
```

Report back: "Lab ready for Q215 — flag placed in /data/local/tmp/"
