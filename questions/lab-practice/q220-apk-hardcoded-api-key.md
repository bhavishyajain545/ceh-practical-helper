# Q220 — Find Hardcoded API Key in APK

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `apktool, jadx, grep` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Decompile `secure_app.apk` and search for hardcoded API keys, tokens, or secrets. Report any API keys found and their variable names."

---

## 🎯 Flag Format

```
variable=<name>; api_key=<value>
```

---

## 💡 Hints

**Hint 1**

`grep -ri "api_key\|api.key\|apikey\|secret\|token" decompiled/`

**Hint 2**

Check `strings.xml`, `BuildConfig.java`, and any Constants class.

---

## ✅ Solution

```bash
apktool d secure_app.apk -o decompiled
grep -rn "API_KEY\|api_key\|secret" decompiled/
# Or with jadx
jadx -d jadx_out/ secure_app.apk
grep -rn "API_KEY\|apiKey\|SECRET" jadx_out/sources/
```

**Answer:** `variable=API_KEY; api_key=sk-CEH-2024-EXPOSED-KEY`

---

## 🤖 Claude Setup Prompt

Provide APK with hardcoded API key in BuildConfig or Constants class.

Report back: "Lab ready for Q220 — secure_app.apk with hardcoded API key ready"
