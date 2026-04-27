# Q178 — Crack SHA-1 Hash Using Hashcat

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashcat` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A SHA-1 hash `7c222fb2927d828af22f592134e8932480637c0d` needs to be cracked. Use hashcat with `rockyou.txt` wordlist. Report the original password."

---

## 🎯 Flag Format

```
password=<string>
```

Example: `password=cracked123`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

SHA-1 ka hashcat mode `100` hai.

**Hint 2**

`hashcat -m 100 hash.txt /usr/share/wordlists/rockyou.txt`

---

## ✅ Solution

```bash
echo "7c222fb2927d828af22f592134e8932480637c0d" > hash.txt
hashcat -m 100 hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m 100 hash.txt --show
```

**Answer:** `password=charmed`

---

## 🤖 Claude Setup Prompt

```bash
echo "7c222fb2927d828af22f592134e8932480637c0d" > hash.txt
```

Report back: "Lab ready for Q178 — SHA-1 hash ready for hashcat cracking"
