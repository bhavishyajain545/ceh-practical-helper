# Q124 — zsteg PNG Stego

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q124".
```
