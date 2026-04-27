# Q213 — Pull File from Android Device

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟢 Easy |
| **Tools** | `adb` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Pull the file `/sdcard/Download/secret_notes.txt` from the Android device to your local machine. Report the flag inside the file."

---

## 🎯 Flag Format

```
flag=<string>
```

---

## 💡 Hints

**Hint 1**

`adb pull /sdcard/Download/secret_notes.txt .`

**Hint 2**

After pulling, `cat secret_notes.txt` locally.

---

## ✅ Solution

```bash
adb pull /sdcard/Download/secret_notes.txt .
cat secret_notes.txt
```

**Answer:** `flag=ADB_PULL_SUCCESS`

---

## 🤖 Claude Setup Prompt

```bash
adb shell "echo 'ADB_PULL_SUCCESS' > /sdcard/Download/secret_notes.txt"
```

Report back: "Lab ready for Q213 — secret_notes.txt placed on Android device"
