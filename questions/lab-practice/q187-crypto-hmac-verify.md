# Q187 — Generate and Verify HMAC Signature

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Generate an HMAC-SHA256 signature for the message `CEH Practical Exam` using key `secretkey123`. Report the HMAC value."

---

## 🎯 Flag Format

```
hmac=<hex_value>
```

Example: `hmac=a1b2c3d4...`

---

## 💡 Hints

**Hint 1**

`echo -n "message" | openssl dgst -sha256 -hmac "key"`

**Hint 2**

`-hmac` flag ke baad key dena hai — quotes mein.

---

## ✅ Solution

```bash
echo -n "CEH Practical Exam" | openssl dgst -sha256 -hmac "secretkey123"
```

**Answer:** `hmac=<computed_hmac_value>`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q187 — HMAC generation exercise"
