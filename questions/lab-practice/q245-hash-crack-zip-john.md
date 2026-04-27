# Q245 — Crack Password Protected ZIP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john, zip2john` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Crack password-protected `secret.zip` using zip2john and John."

---

## 🎯 Flag Format

```
password=<zip_password>; flag=<contents>
```

---

## ✅ Solution

```bash
zip2john secret.zip > zip_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
john --show zip_hash.txt
```

**Answer:** `password=letmein; flag=ZIP_CRACKED_FLAG`

---

## 🤖 Claude Setup Prompt

```bash
echo "ZIP_CRACKED_FLAG" > /tmp/flag.txt
zip -P letmein secret.zip /tmp/flag.txt
```

Report back: "Lab ready for Q245"
