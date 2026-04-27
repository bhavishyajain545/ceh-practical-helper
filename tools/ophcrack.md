# ophcrack — Windows Password Cracker (Rainbow Tables)

> "Cracks Windows passwords using rainbow tables. Faster than brute force for LM/NTLM hashes."

**Install check:** `ophcrack --help` or `sudo apt install ophcrack`

---

## 🎯 Cheat-flow

| You need to... | Command / Action |
|---|---|
| Crack with CLI | `ophcrack -d /path/to/tables -t /path/to/tables -f hashes.txt` |
| Launch GUI | `ophcrack` (from menu or terminal) |
| Load hashes in GUI | Load → PWDUMP file / SAM + SYSTEM |
| Crack in GUI | Click "Crack" button |

---

## 🔑 Key Concepts

- **Rainbow tables** = precomputed hash-to-password lookup tables
- Much faster than brute force but needs tables downloaded
- Works on **LM and NTLM** hashes (Windows passwords)
- Tables: XP free small/medium, Vista free, etc.

---

## 📋 Recipes

```bash
# 1. CLI mode with tables
ophcrack -d /usr/share/ophcrack/tables -t /usr/share/ophcrack/tables -f pwdump.txt

# 2. GUI mode
ophcrack
# → Load → Select PWDUMP file
# → Tables → Select/install tables  
# → Click Crack

# 3. Using with SAM dump
# First extract SAM + SYSTEM from Windows:
# reg save HKLM\SAM sam.bak
# reg save HKLM\SYSTEM system.bak
# Then load in ophcrack GUI
```

---

## 💡 Exam Tips

- ophcrack is for **Windows** password hashes only
- Rainbow table attack is DIFFERENT from dictionary attack:
  - Dictionary: hash each word, compare → slow
  - Rainbow: precomputed lookup → fast but needs storage
- Free tables crack simple passwords; paid tables cover more
- If password is complex, rainbow tables may not have it — use hashcat instead
- GUI is easier than CLI for exam

---

## ⚠️ Gotchas

- Rainbow tables need to be downloaded separately — several GB each
- LM hashes split password into 7-char halves — easier to crack
- Modern Windows disables LM hashes — only NTLM available
- If tables not installed, ophcrack wont crack anything

---

## 🔗 Related

- [hashcat.md](hashcat.md) — GPU-based cracking (more powerful)
- [john.md](john.md) — CPU-based dictionary/brute force
- [mimikatz.md](mimikatz.md) — extract hashes from Windows memory
