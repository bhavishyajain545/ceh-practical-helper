# Q214 — Push Payload File to Android Device

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟢 Easy |
| **Tools** | `adb` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Create a test file `payload.txt` with content `CEH_PAYLOAD_DELIVERED` and push it to `/sdcard/Download/` on the Android device. Verify the file was transferred successfully."

---

## 🎯 Flag Format

```
pushed=<yes|no>; location=<path>
```

---

## 💡 Hints

**Hint 1**

`adb push local_file remote_path`

**Hint 2**

Verify: `adb shell cat /sdcard/Download/payload.txt`

---

## ✅ Solution

```bash
echo "CEH_PAYLOAD_DELIVERED" > payload.txt
adb push payload.txt /sdcard/Download/
adb shell cat /sdcard/Download/payload.txt
```

**Answer:** `pushed=yes; location=/sdcard/Download/payload.txt`

---

## 🤖 Claude Setup Prompt

Ensure device connected via ADB.

Report back: "Lab ready for Q214 — Android device ready for file push"
