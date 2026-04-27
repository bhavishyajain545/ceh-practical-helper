# Q240 — Identify MD5-Crypt Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify: `$1$salt$qJH7.N4xYta3aEG/dfqo/0`. What hash type? What does `$1$` mean? Hashcat mode?"

---

## 🎯 Flag Format

```
type=<hash_type>; prefix=<$N$>; hashcat_mode=<number>
```

---

## ✅ Solution

```bash
hashid '$1$salt$qJH7.N4xYta3aEG/dfqo/0' -m
```

`$1$` = MD5-crypt (older Linux systems). Hashcat mode = 500.

**Answer:** `type=md5crypt; prefix=$1$; hashcat_mode=500`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q240"
