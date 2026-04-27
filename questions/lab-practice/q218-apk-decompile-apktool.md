# Q218 — Decompile APK Using apktool

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `apktool` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Decompile `target.apk` using apktool. Navigate the decompiled directory structure and find: (1) The app's package name from AndroidManifest.xml, (2) The main activity name, (3) Any hardcoded strings in `res/values/strings.xml`."

---

## 🎯 Flag Format

```
package=<name>; main_activity=<name>; flag=<string>
```

---

## 💡 Hints

**Hint 1**

`apktool d target.apk -o decompiled/`

**Hint 2**

`grep "package=" decompiled/AndroidManifest.xml` aur `cat decompiled/res/values/strings.xml`

---

## ✅ Solution

```bash
apktool d target.apk -o decompiled
grep 'package=' decompiled/AndroidManifest.xml
grep 'MAIN' -A2 decompiled/AndroidManifest.xml
cat decompiled/res/values/strings.xml | grep -i flag
```

**Answer:** `package=com.ceh.target; main_activity=MainActivity; flag=APK_DECOMPILED`

---

## 🤖 Claude Setup Prompt

Provide a sample APK with a flag in strings.xml or pull from device:
```bash
adb pull /data/app/com.ceh.target/base.apk target.apk
```

Report back: "Lab ready for Q218 — target.apk ready for decompilation"
