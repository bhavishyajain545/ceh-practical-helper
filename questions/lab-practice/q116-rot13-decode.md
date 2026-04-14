# Q116 — ROT13 Cipher

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `tr` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Decode `Pbatenghyngvbaf` using ROT13.

---

## 🎯 Flag Format

```
plain=<text>
```

Example: `plain=Congratulations`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`tr 'A-Za-z' 'N-ZA-Mn-za-m'`
</details>

<details>
<summary>Hint 2</summary>

Reciprocal cipher.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo 'Pbatenghyngvbaf' | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

Decodes to `Congratulations`.

**Answer:** `plain=Congratulations`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q116:
1. Local Parrot exercise — no VM interaction.
2. which tr — /usr/bin/tr expected (coreutils).
3. Ciphertext inline in Q.

Report back: "Lab ready for Q116 — tr available on Parrot".
```
