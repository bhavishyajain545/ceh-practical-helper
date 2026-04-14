# Q112 — Steghide Extract

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `steghide` |
| **Time budget** | 10–15 min |

---

## 📝 Question

You receive `secret.jpg` with passphrase `cehlab`. Extract hidden data.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=steghide extract -sf secret.jpg -p cehlab`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-sf` source file, `-p` passphrase.
</details>

<details>
<summary>Hint 2</summary>

Output goes to current dir.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
steghide extract -sf secret.jpg -p cehlab
```

Extracts hidden file.

**Answer:** `cmd=steghide extract -sf secret.jpg -p cehlab`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q112:
1. Local Parrot exercise (or file delivered via scp).
2. On Parrot: sudo apt install steghide.
3. Stage test file: create carrier JPEG (cp /usr/share/pixmaps/*.jpg secret.jpg) and embed: echo 'flag' > msg.txt; steghide embed -cf secret.jpg -ef msg.txt -p cehlab.

Report back: "Lab ready for Q112 — steghide installed, secret.jpg with embedded data (pwd=cehlab)".
```
