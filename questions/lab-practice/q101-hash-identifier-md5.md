# Q101 — Identify Unknown Hash

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hash-identifier` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use `hash-identifier` on `5f4dcc3b5aa765d61d8327deb882cf99`. What hash type?

---

## 🎯 Flag Format

```
hash_type=<name>
```

Example: `hash_type=MD5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Tool prints possible types.
</details>

<details>
<summary>Hint 2</summary>

32-char hex = likely MD5.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo '5f4dcc3b5aa765d61d8327deb882cf99' | hash-identifier
```

Identifies as MD5 (and password is `password`).

**Answer:** `hash_type=MD5`

📖 Ref: [tools/hash-identifier.md](../../tools/hash-identifier.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q101:
1. Local Parrot exercise.
2. which hash-identifier (or hashid) — sudo apt install hash-identifier if missing.

Report back: "Lab ready for Q101 — hash-identifier installed on Parrot".
```
