# Q232 — Identify SHA-1 Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hash-identifier, hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify: `aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d`. What hash type is this? How many characters long is it?"

---

## 🎯 Flag Format

```
type=<hash_type>; length=<chars>
```

---

## 💡 Hints

**Hint 1**

40 hex characters = SHA-1.

**Hint 2**

`echo -n '<hash>' | wc -c` for length.

---

## ✅ Solution

```bash
hashid 'aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d'
echo -n 'aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d' | wc -c
```

**Answer:** `type=sha1; length=40`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q232"
