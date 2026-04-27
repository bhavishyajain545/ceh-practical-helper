# Q238 — Identify MySQL Password Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify: `*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9`. What hash type? Note the leading asterisk. Hashcat mode?"

---

## 🎯 Flag Format

```
type=<hash_type>; hashcat_mode=<number>
```

---

## ✅ Solution

```bash
hashid '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' -m
```

Leading `*` + 40 hex chars = MySQL 4.1+ (SHA1(SHA1(password))). Hashcat mode = 300.

**Answer:** `type=mysql-sha1; hashcat_mode=300`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q238"
