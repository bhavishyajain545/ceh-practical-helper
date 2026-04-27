# Q171 — Generate MD5 Hash of a String

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `md5sum, echo` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Generate the MD5 hash of the string `CEHv12Practical`. Report the full hash value."

---

## 🎯 Flag Format

```
hash=<md5_value>
```

Example: `hash=d41d8cd98f00b204e9800998ecf8427e`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`echo -n` use karo — `-n` bina newline ke string dega.

**Hint 2**

`echo -n "CEHv12Practical" | md5sum`

---

## ✅ Solution

```bash
echo -n "CEHv12Practical" | md5sum
```

**Answer:** `hash=<computed_md5_value>`

---

## 🤖 Claude Setup Prompt

No setup needed — this uses built-in commands.

Report back: "Lab ready for Q171 — md5sum available"
