# Q152 — Extract Hidden Message from JPEG Using Steghide

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `steghide` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"A file `landscape.jpg` contains a hidden embedded file. The passphrase is `cehpass123`. Extract the hidden file and report the flag inside it."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=HIDDEN_MESSAGE`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`steghide extract -sf <file>` command use karo.

**Hint 2**

Passphrase prompt aayega — `cehpass123` dalo.

---

## ✅ Solution

```bash
steghide extract -sf landscape.jpg -p cehpass123
cat secret.txt
```

**Answer:** `flag=STEG_HIDE_EXTRACTED`

---

## 🤖 Claude Setup Prompt

1. Create a JPEG and embed a secret file:
```bash
convert -size 300x300 xc:green /tmp/landscape.jpg
echo "STEG_HIDE_EXTRACTED" > /tmp/secret.txt
steghide embed -cf /tmp/landscape.jpg -ef /tmp/secret.txt -p cehpass123
```
2. Place `landscape.jpg` in working directory. Remove `secret.txt`.

Report back: "Lab ready for Q152 — landscape.jpg with steghide embedded secret ready"
