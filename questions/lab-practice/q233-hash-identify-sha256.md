# Q233 — Identify SHA-256 Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify: `2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824`. What hash type? What is the hashcat mode number for this type?"

---

## 🎯 Flag Format

```
type=<hash_type>; hashcat_mode=<number>
```

---

## 💡 Hints

**Hint 1**

64 hex characters = SHA-256.

**Hint 2**

Hashcat mode 1400 = SHA-256.

---

## ✅ Solution

```bash
hashid '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824' -m
```

**Answer:** `type=sha256; hashcat_mode=1400`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q233"
