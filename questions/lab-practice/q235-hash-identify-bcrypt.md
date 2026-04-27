# Q235 — Identify Bcrypt Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify: `$2a$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy`. What hash type? What is the cost factor? What is the hashcat mode?"

---

## 🎯 Flag Format

```
type=<hash_type>; cost=<number>; hashcat_mode=<number>
```

---

## 💡 Hints

**Hint 1**

`$2a$` prefix = bcrypt.

**Hint 2**

Cost factor is the number after second `$`. Hashcat mode = 3200.

---

## ✅ Solution

```bash
hashid '$2a$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy'
```

**Answer:** `type=bcrypt; cost=10; hashcat_mode=3200`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q235"
