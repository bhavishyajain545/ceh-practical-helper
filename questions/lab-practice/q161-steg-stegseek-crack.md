# Q161 — Crack Steghide Passphrase Using Stegseek

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `stegseek, steghide` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A JPEG `locked_image.jpg` has data hidden with steghide but the passphrase is unknown. Use stegseek with `rockyou.txt` wordlist to crack the passphrase and extract the hidden file. Report the passphrase and the flag."

---

## 🎯 Flag Format

```
passphrase=<string>; flag=<string>
```

Example: `passphrase=password123; flag=CRACKED_STEG`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`stegseek locked_image.jpg /usr/share/wordlists/rockyou.txt`

**Hint 2**

Stegseek bahut fast hai — rockyou.txt se seconds mein crack karega.

---

## ✅ Solution

```bash
stegseek locked_image.jpg /usr/share/wordlists/rockyou.txt
cat locked_image.jpg.out
```

**Answer:** `passphrase=sunshine; flag=STEGSEEK_CRACKED`

---

## 🤖 Claude Setup Prompt

1. Create image with steghide using weak passphrase:
```bash
convert -size 300x300 xc:orange /tmp/locked_image.jpg
echo "STEGSEEK_CRACKED" > /tmp/secret.txt
steghide embed -cf /tmp/locked_image.jpg -ef /tmp/secret.txt -p sunshine -f
cp /tmp/locked_image.jpg locked_image.jpg
```

Report back: "Lab ready for Q161 — locked_image.jpg with crackable steghide passphrase ready"
