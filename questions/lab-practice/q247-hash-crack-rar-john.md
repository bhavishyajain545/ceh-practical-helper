# Q247 — Crack RAR Archive Password

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john, rar2john` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Crack the password of `archive.rar` using rar2john and John."

---

## 🎯 Flag Format

```
password=<rar_password>
```

---

## ✅ Solution

```bash
rar2john archive.rar > rar_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt
john --show rar_hash.txt
```

**Answer:** `password=dragon`

---

## 🤖 Claude Setup Prompt

```bash
echo "RAR_CRACKED" > /tmp/secret.txt
rar a -hp"dragon" archive.rar /tmp/secret.txt
```

Report back: "Lab ready for Q247"
