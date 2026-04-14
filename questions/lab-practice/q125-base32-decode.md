# Q125 — Base32 Decode

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `base32 (coreutils)` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Decode `IVBEG===` from base32.

---

## 🎯 Flag Format

```
plain=<text>
```

Example: `plain=EH`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`base32 -d`
</details>

<details>
<summary>Hint 2</summary>

Padding `=` matters.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo 'IVBEG===' | base32 -d
```

Decodes to short ASCII.

**Answer:** `plain=EH`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q125:
1. Local Parrot exercise.
2. which base32.

Report back: "Lab ready for Q125 — base32 available".
```
