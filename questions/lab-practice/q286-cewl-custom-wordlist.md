# Q286 — Generate Custom Wordlist Using CeWL

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `cewl` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Use CeWL to spider the target website and generate a custom wordlist. Set minimum word length to 6. Use the generated wordlist for brute forcing."

---

## 🎯 Flag Format

```
words_generated=<number>; min_length=<number>
```

---

## ✅ Solution

```bash
cewl http://192.168.52.129 -m 6 -w custom_wordlist.txt
wc -l custom_wordlist.txt
head -20 custom_wordlist.txt
```

**Answer:** `words_generated=<count>; min_length=6`

---

## 🤖 Claude Setup Prompt

Ensure web server running on target. Report back: "Lab ready for Q286"
