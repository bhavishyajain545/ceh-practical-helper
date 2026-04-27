# Q246 — Crack Password Protected PDF

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john, pdf2john` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Crack protected PDF `confidential.pdf` using pdf2john and John."

---

## 🎯 Flag Format

```
password=<pdf_password>
```

---

## ✅ Solution

```bash
pdf2john.pl confidential.pdf > pdf_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt pdf_hash.txt
john --show pdf_hash.txt
```

**Answer:** `password=qwerty`

---

## 🤖 Claude Setup Prompt

Create password-protected PDF with password qwerty. Report back: "Lab ready for Q246"
