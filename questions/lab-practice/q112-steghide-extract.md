# Q112 — Steghide Extract

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q112".
```
