# Q115 — CyberChef Base64 Decode

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `base64 (coreutils)` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Decode `Q0VIIFByYWN0aWNhbA==` using base64. Provide plaintext.

---

## 🎯 Flag Format

```
plain=<text>
```

Example: `plain=CEH Practical`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`echo Q0VIIFByYWN0aWNhbA== | base64 -d`
</details>

<details>
<summary>Hint 2</summary>

Standard base64.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo 'Q0VIIFByYWN0aWNhbA==' | base64 -d
```

Decodes to `CEH Practical`.

**Answer:** `plain=CEH Practical`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q115:
1. Local Parrot exercise.
2. which base64 — /usr/bin/base64 default.

Report back: "Lab ready for Q115 — base64 coreutils available".
```
