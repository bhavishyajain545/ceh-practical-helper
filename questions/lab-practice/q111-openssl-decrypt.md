# Q111 — OpenSSL AES Decrypt

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl` |
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
Pre-requisites for Q111:
1. Local Parrot exercise.
2. Pre-generate a cipher.b64 file: echo 'secret' | openssl enc -aes-256-cbc -pbkdf2 -salt -a -k lab123 > cipher.b64.
3. which openssl.

Report back: "Lab ready for Q111 — openssl available, cipher.b64 staged in cwd".
```
