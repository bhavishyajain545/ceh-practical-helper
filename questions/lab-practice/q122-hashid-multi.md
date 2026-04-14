# Q122 — hashid Multi-hash Detection

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hashid` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `hashid` on `$2a$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/LewKyAO.uuQxJ3F6i` to identify the hash type.

---

## 🎯 Flag Format

```
hash_type=<name>
```

Example: `hash_type=bcrypt`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`$2y$` prefix → bcrypt.
</details>

<details>
<summary>Hint 2</summary>

hashid prints possibilities ranked.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hashid '$2a$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/LewKyAO.uuQxJ3F6i'
```

Identifies **bcrypt** (60-char string, `$2a$`/`$2b$`/`$2y$` prefix, 22-char salt + 31-char hash). This sample hash corresponds to the password `password`.

**Answer:** `hash_type=bcrypt`

📖 Ref: [tools/hashid.md](../../tools/hashid.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q122:
1. Local Parrot exercise.
2. which hashid (sudo apt install hashid if missing).

Report back: "Lab ready for Q122 — hashid installed on Parrot".
```
