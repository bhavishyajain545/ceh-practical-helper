# Q226 — Capture Screenshot via ADB

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟢 Easy |
| **Tools** | `adb` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Capture a screenshot and a 10-second screen recording from the Android device using ADB. Save them locally. Report the file sizes."

---

## 🎯 Flag Format

```
screenshot=<size_kb>; recording=<size_kb>
```

---

## 💡 Hints

**Hint 1**

Screenshot: `adb shell screencap /sdcard/screen.png && adb pull /sdcard/screen.png`

**Hint 2**

Recording: `adb shell screenrecord --time-limit 10 /sdcard/video.mp4 && adb pull /sdcard/video.mp4`

---

## ✅ Solution

```bash
adb shell screencap /sdcard/screen.png
adb pull /sdcard/screen.png
adb shell screenrecord --time-limit 10 /sdcard/video.mp4
adb pull /sdcard/video.mp4
ls -la screen.png video.mp4
```

**Answer:** `screenshot=<size>; recording=<size>`

---

## 🤖 Claude Setup Prompt

Ensure Android device connected.

Report back: "Lab ready for Q226 — Android device ready for screen capture"
