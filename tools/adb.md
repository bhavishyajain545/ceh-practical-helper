# adb — Android Debug Bridge

> **The Android device/emulator remote.** Push/pull files, install APKs, open shells, screenshot, forward ports.

**Install check:** `adb version`

---

## 🎯 Cheat-flow

```bash
adb devices                                # list connected
adb connect 10.10.10.5:5555                # network ADB
adb -s <serial> ...                        # target specific device
adb install -r app.apk                     # install (reinstall if present)
adb uninstall com.example.app
adb shell                                  # interactive shell
adb shell pm list packages                 # list installed apps
adb shell pm path com.example              # where's the APK
adb pull /data/app/com.example.apk .       # pull binary
adb push local /sdcard/remote              # push file
adb logcat                                 # live logs
adb logcat *:E                             # errors only
adb shell am start -n com.example/.MainActivity
adb shell am start -a android.intent.action.VIEW -d "http://..."
adb shell dumpsys package com.example
adb shell run-as com.example               # shell as app (debuggable)
adb forward tcp:1337 tcp:1337              # port forward
adb reverse tcp:8080 tcp:8080              # reverse (device → host)
adb shell screencap -p /sdcard/s.png && adb pull /sdcard/s.png
```

---

## 🔑 Common subcommands

| Command | Purpose |
|---|---|
| `adb devices` | Connected targets |
| `adb connect ip:port` | TCP ADB |
| `adb shell` | `/system/bin/sh` on device |
| `adb install [-r] [-t] [-d] app.apk` | Install APK (`-t` test, `-d` downgrade) |
| `adb uninstall [-k] pkg` | Uninstall (`-k` keep data) |
| `adb pull <remote> [local]` | Download from device |
| `adb push <local> <remote>` | Upload |
| `adb logcat [-d] [-c]` | Logs (`-d` dump-and-exit, `-c` clear) |
| `adb shell pm ...` | Package manager |
| `adb shell am ...` | Activity manager |
| `adb shell dumpsys <svc>` | Dump service state |
| `adb shell settings get/put` | Settings |
| `adb reboot [bootloader|recovery]` | Reboot |
| `adb root` | Restart adbd as root (dev builds) |

---

## 📋 Recipes

```bash
# 1. Pull an installed APK for analysis
adb shell pm path com.example.app
# package:/data/app/com.example.app-1/base.apk
adb pull /data/app/com.example.app-1/base.apk .

# 2. Grab app's private data (debuggable apps only)
adb shell run-as com.example.app tar -c ./ > app_data.tar

# 3. Find exported activities
adb shell dumpsys package com.example.app | grep -A1 "Activity Resolver"

# 4. Start a hidden activity via intent
adb shell am start -n com.example.app/.SecretActivity

# 5. Enable network ADB over Wi-Fi
adb tcpip 5555
adb connect 10.0.0.42:5555
```

---

## ⚠️ Gotchas

- Device must have **USB debugging enabled** (Developer options).
- `unauthorized` in `adb devices` → accept the RSA prompt on the device.
- `adb root` only works on userdebug/eng builds, not retail.
- `run-as` requires the app to be marked debuggable in its manifest.
- Pulled APKs may be multi-split (`base.apk + split_config.*`) — pull them all.

---

## 🔗 Related

- [apktool](apktool.md) · [jadx](jadx.md) · [mobsf](mobsf.md) · [dex2jar](dex2jar.md)
