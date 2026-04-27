# Q183 — Decode ROT13/Caesar Cipher

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `tr, python3` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"The following message is ROT13 encoded: `PRU{ebg13_qrpbqrq_synt}`. Decode it and report the flag."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=CEH{decoded_flag}`

---

## 💡 Hints

**Hint 1**

ROT13 = each letter 13 positions shift. `tr` command se ek line mein ho jayega.

**Hint 2**

`echo "text" | tr 'A-Za-z' 'N-ZA-Mn-za-m'`

---

## ✅ Solution

```bash
echo "PRU{ebg13_qrpbqrq_synt}" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

**Answer:** `flag=CEH{rot13_decoded_flag}`

---

## 🤖 Claude Setup Prompt

No setup needed — built-in command.

Report back: "Lab ready for Q183 — ROT13 decode exercise"
