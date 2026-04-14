# Q123 — Steghide Info Probe

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `steghide` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `steghide info secret.jpg` (no passphrase). What does it report when capacity is unknown?

---

## 🎯 Flag Format

```
info_field=<text>
```

Example: `info_field=capacity`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Prompts for passphrase to read embedded data.
</details>

<details>
<summary>Hint 2</summary>

Without passphrase: shows file format + capacity only.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
steghide info secret.jpg
```

Lists format and embed capacity.

**Answer:** `info_field=capacity`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q123:
1. Local Parrot exercise.
2. Same staging as Q112 — secret.jpg with embedded data.
3. 'steghide info' with no password will prompt but still display capacity/format.

Report back: "Lab ready for Q123 — steghide installed, secret.jpg staged".
```
