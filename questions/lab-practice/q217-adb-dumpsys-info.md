# Q217 — Extract Device Info Using dumpsys

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Use ADB dumpsys to extract: (1) Battery level, (2) WiFi SSID connected to, (3) Number of running activities. Report the findings."

---

## 🎯 Flag Format

```
battery=<percent>; wifi_ssid=<name>; activities=<number>
```

---

## 💡 Hints

**Hint 1**

`adb shell dumpsys battery` — battery info. `adb shell dumpsys wifi` — WiFi info.

**Hint 2**

`adb shell dumpsys activity activities | grep -c "Running activities"`

---

## ✅ Solution

```bash
adb shell dumpsys battery | grep level
adb shell dumpsys wifi | grep "mWifiInfo" | grep SSID
adb shell dumpsys activity activities | grep "Running activities"
```

**Answer:** `battery=100; wifi_ssid=AndroidWifi; activities=3`

---

## 🤖 Claude Setup Prompt

Ensure Android emulator running with network.

Report back: "Lab ready for Q217 — Android device with dumpsys access"
