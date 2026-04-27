# Q186 — Identify Bcrypt Hash and Understand Salt

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hash-identifier, hashid` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Identify the hash type and cost factor of: `$2b$12$LJ3m4ys3Lg2VBe4WMYOYfuGBkDNcpN6SI.E0IrKqL6FmpPgXKb2Cu`. What is the salt portion?"

---

## 🎯 Flag Format

```
type=<hash_type>; cost=<number>; salt=<salt_portion>
```

Example: `type=bcrypt; cost=12; salt=LJ3m4ys3Lg2VBe4WMYOYfu`

---

## 💡 Hints

**Hint 1**

`$2b$` prefix = bcrypt. Number after second `$` is cost factor.

**Hint 2**

Bcrypt salt is the first 22 characters after `$2b$12$`.

---

## ✅ Solution

```bash
hashid '$2b$12$LJ3m4ys3Lg2VBe4WMYOYfuGBkDNcpN6SI.E0IrKqL6FmpPgXKb2Cu'
```

- `$2b$` → bcrypt
- `12` → cost/rounds
- First 22 chars after `$12$` → salt

**Answer:** `type=bcrypt; cost=12; salt=LJ3m4ys3Lg2VBe4WMYOYfu`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q186 — bcrypt hash identification exercise"
