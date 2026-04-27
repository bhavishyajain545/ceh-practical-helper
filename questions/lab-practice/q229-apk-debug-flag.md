# Q229 — Check if APK is Debuggable

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `apktool, aapt` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Check if `vulnerable.apk` has the debuggable flag set to true in its manifest. Report the debuggable status and the target SDK version."

---

## 🎯 Flag Format

```
debuggable=<true|false>; target_sdk=<version>
```

---

## 💡 Hints

**Hint 1**

`aapt dump badging vulnerable.apk | grep -i debug`

**Hint 2**

Or decompile: `apktool d vulnerable.apk && grep debuggable AndroidManifest.xml`

---

## ✅ Solution

```bash
aapt dump badging vulnerable.apk | grep -i "debuggable\|sdkVersion"
# Or
apktool d vulnerable.apk -o vuln_decomp
grep "debuggable" vuln_decomp/AndroidManifest.xml
```

**Answer:** `debuggable=true; target_sdk=33`

---

## 🤖 Claude Setup Prompt

Provide APK with `android:debuggable="true"` in manifest.

Report back: "Lab ready for Q229 — vulnerable.apk with debuggable flag ready"
