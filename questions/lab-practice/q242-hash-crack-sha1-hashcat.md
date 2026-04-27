# Q242 — Crack SHA-1 Hash Using Hashcat

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashcat` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Crack SHA-1 hash `5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8` using hashcat mode 100."

---

## 🎯 Flag Format

```
password=<cracked_password>
```

---

## ✅ Solution

```bash
echo "5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8" > sha1.txt
hashcat -m 100 sha1.txt /usr/share/wordlists/rockyou.txt
hashcat -m 100 sha1.txt --show
```

**Answer:** `password=password`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q242"
