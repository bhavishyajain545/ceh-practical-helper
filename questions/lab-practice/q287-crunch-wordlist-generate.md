# Q287 — Generate Targeted Wordlist Using Crunch

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `crunch` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Use Crunch to generate a wordlist of all 4-digit PINs (0000-9999) and all 3-character lowercase combinations."

---

## 🎯 Flag Format

```
pin_count=<number>; alpha_count=<number>
```

---

## ✅ Solution

```bash
# 4-digit PINs
crunch 4 4 0123456789 -o pins.txt
wc -l pins.txt
# 3-char lowercase
crunch 3 3 abcdefghijklmnopqrstuvwxyz -o alpha3.txt
wc -l alpha3.txt
```

**Answer:** `pin_count=10000; alpha_count=17576`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q287"
