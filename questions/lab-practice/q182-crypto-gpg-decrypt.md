# Q182 — Decrypt GPG Encrypted File

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `gpg` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"A GPG encrypted file `message.gpg` is provided. The passphrase is `unlock2024`. Decrypt and report the flag inside."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=GPG_DECRYPTED`

---

## 💡 Hints

**Hint 1**

`gpg -d message.gpg` ya `gpg --decrypt message.gpg`

**Hint 2**

`gpg --batch --passphrase unlock2024 -d message.gpg`

---

## ✅ Solution

```bash
gpg --batch --passphrase unlock2024 -d message.gpg
```

**Answer:** `flag=GPG_SECRET_REVEALED`

---

## 🤖 Claude Setup Prompt

```bash
echo "GPG_SECRET_REVEALED" > /tmp/msg.txt
gpg --batch --passphrase unlock2024 -c /tmp/msg.txt
mv /tmp/msg.txt.gpg message.gpg
```

Report back: "Lab ready for Q182 — message.gpg ready for decryption"
