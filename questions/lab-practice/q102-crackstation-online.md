# Q102 — CrackStation Lookup (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
| **Time budget** | 10–20 min |

---

## 📝 Question

You have hash `e10adc3949ba59abbe56e057f20f883e`. What plaintext does CrackStation return?

---

## 🎯 Flag Format

```
plaintext=<word>
```

Example: `plaintext=123456`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

MD5 of `123456`.
</details>

<details>
<summary>Hint 2</summary>

Common-as-dirt.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Visit crackstation.net and paste
```

Returns `123456`.

**Answer:** `plaintext=123456`

📖 Ref: [playbooks/hash-cracking-playbook.md](../../playbooks/hash-cracking-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q102".
```
