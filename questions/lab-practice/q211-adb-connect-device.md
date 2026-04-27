# Q211 — Connect to Android Device via ADB

| Field | Value |
|-------|-------|
| **Target** | Android emulator `192.168.52.131:5555` |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟢 Easy |
| **Tools** | `adb` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Connect to the Android device at `192.168.52.131:5555` using ADB. Verify connection and report the device model and Android version."

---

## 🎯 Flag Format

```
model=<device_model>; android_version=<version>
```

Example: `model=Pixel_4; android_version=11`

---

## 💡 Hints

**Hint 1**

`adb connect 192.168.52.131:5555`

**Hint 2**

`adb shell getprop ro.product.model` aur `adb shell getprop ro.build.version.release`

---

## ✅ Solution

```bash
adb connect 192.168.52.131:5555
adb devices
adb shell getprop ro.product.model
adb shell getprop ro.build.version.release
```

**Answer:** `model=sdk_gphone64_x86_64; android_version=13`

---

## 🤖 Claude Setup Prompt

1. Start Android emulator with ADB over network enabled.
2. Ensure ADB port 5555 is accessible from Parrot.

Report back: "Lab ready for Q211 — Android device connected via ADB at 192.168.52.131:5555"
