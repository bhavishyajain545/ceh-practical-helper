# Q101 — Identify Unknown Hash

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q101".
```
