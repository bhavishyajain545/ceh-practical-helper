# Q174 — Encode Message in Base64

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `base64` |
| **Time budget** | 2–3 min |

---

## 📝 Question

"Encode the string `EthicalHacking2024` in Base64. Then decode it back to verify. Report the encoded value."

---

## 🎯 Flag Format

```
encoded=<base64_value>
```

Example: `encoded=SGVsbG8gV29ybGQ=`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`echo -n "string" | base64`

**Hint 2**

Verify: `echo "encoded_value" | base64 -d`

---

## ✅ Solution

```bash
echo -n "EthicalHacking2024" | base64
# Verify
echo "RXRoaWNhbEhhY2tpbmcyMDI0" | base64 -d
```

**Answer:** `encoded=RXRoaWNhbEhhY2tpbmcyMDI0`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q174 — base64 encode exercise"
