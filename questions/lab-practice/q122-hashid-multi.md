# Q122 — hashid Multi-hash Detection

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q122".
```
