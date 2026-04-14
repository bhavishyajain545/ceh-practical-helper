# Q102 — CrackStation Lookup (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟢 Easy |
| **Tools** | `browser (crackstation.net)` |
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
Pre-requisites for Q102:
1. Conceptual — requires internet from Parrot.
2. Verify DNS+HTTPS out: curl -sI https://crackstation.net.

Report back: "Lab ready for Q102 — Parrot internet access to crackstation.net".
```
