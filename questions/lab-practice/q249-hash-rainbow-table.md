# Q249 — Rainbow Table Hash Lookup

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `ophcrack` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Using ophcrack, crack the NTLM hash: `a4f49c406510bdcab6824ee7c30fd852`. What is the difference between rainbow table attack and dictionary attack?"

---

## 🎯 Flag Format

```
password=<cracked>; method=<rainbow|dictionary>
```

---

## ✅ Solution

```bash
ophcrack -d /usr/share/ophcrack/tables -f hash.txt
```

Rainbow tables = precomputed hash-to-password mappings. Faster than brute force but require storage.

**Answer:** `password=password; method=rainbow`

---

## 🤖 Claude Setup Prompt

Ensure ophcrack and rainbow tables installed.

Report back: "Lab ready for Q249"
