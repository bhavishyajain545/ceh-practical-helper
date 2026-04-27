# Q236 — Identify SHA-512 Crypt Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify: `$6$rounds=5000$saltsalt$hash...`. What hash type? What does `$6$` indicate? What is the hashcat mode?"

---

## 🎯 Flag Format

```
type=<hash_type>; prefix=<$N$>; hashcat_mode=<number>
```

---

## ✅ Solution

```bash
hashid '$6$rounds=5000$saltsalt$IxDD3jeSOb5eB1CX5LBsqZFVkJdido3OUILO5Ifz5iwMuTS4XMS130MT' -m
```

`$6$` = SHA-512 crypt (Linux /etc/shadow). Hashcat mode = 1800.

**Answer:** `type=sha512crypt; prefix=$6$; hashcat_mode=1800`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q236"
