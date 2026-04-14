# Q111 — OpenSSL AES Decrypt

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
| **Time budget** | 10–15 min |

---

## 📝 Question

You have ciphertext base64 (created with `openssl enc -aes-256-cbc -salt -pbkdf2`) and password `lab123`. Provide decrypt command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=openssl enc -d -aes-256-cbc -pbkdf2 -in cipher.b64 -a -k lab123`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-d` decrypt, `-a` base64.
</details>

<details>
<summary>Hint 2</summary>

Match the original kdf flags.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in cipher.b64 -a -k lab123
```

Yields plaintext.

**Answer:** `cmd=openssl enc -d -aes-256-cbc -pbkdf2 -in cipher.b64 -a -k lab123`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q111".
```
