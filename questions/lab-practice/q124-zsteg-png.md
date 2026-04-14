# Q124 — zsteg PNG Stego

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `zsteg` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `zsteg` against `image.png` to find hidden LSB data. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=zsteg image.png`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`gem install zsteg` if missing.
</details>

<details>
<summary>Hint 2</summary>

Looks at LSB and other channels.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
zsteg image.png
```

Reports hidden ASCII strings if any.

**Answer:** `cmd=zsteg image.png`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q124:
1. Local Parrot exercise.
2. zsteg is Ruby gem: sudo gem install zsteg (or sudo apt install zsteg on newer Parrot).
3. Stage image.png (any PNG — zsteg scans LSB).

Report back: "Lab ready for Q124 — zsteg installed, image.png staged".
```
