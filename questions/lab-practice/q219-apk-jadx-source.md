# Q219 — Decompile APK to Java Source Using jadx

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `jadx` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Decompile `target.apk` using jadx to get Java source code. Find the login validation logic in the source code. What are the hardcoded credentials in the login check?"

---

## 🎯 Flag Format

```
user=<username>; pass=<password>
```

---

## 💡 Hints

**Hint 1**

`jadx -d output/ target.apk` — decompile to Java source.

**Hint 2**

`grep -r "password\|login\|credential" output/sources/`

---

## ✅ Solution

```bash
jadx -d jadx_output/ target.apk
grep -rn "password\|credential\|admin" jadx_output/sources/
cat jadx_output/sources/com/ceh/target/LoginActivity.java
```

**Answer:** `user=admin; pass=ceh_secret_2024`

---

## 🤖 Claude Setup Prompt

Provide APK with hardcoded credentials in LoginActivity.

Report back: "Lab ready for Q219 — target.apk ready for jadx decompilation"
