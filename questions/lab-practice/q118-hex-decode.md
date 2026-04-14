# Q118 — Hex String Decode

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `xxd` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Decode hex `434548` to ASCII.

---

## 🎯 Flag Format

```
plain=<text>
```

Example: `plain=CEH`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`xxd -r -p`
</details>

<details>
<summary>Hint 2</summary>

Or `echo | xxd -r -p`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo '434548' | xxd -r -p
```

Decodes to `CEH`.

**Answer:** `plain=CEH`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q118:
1. Local Parrot exercise.
2. which xxd (vim-common package).

Report back: "Lab ready for Q118 — xxd available on Parrot".
```
