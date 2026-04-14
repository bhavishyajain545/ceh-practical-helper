# Q115 — CyberChef Base64 Decode

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q115".
```
