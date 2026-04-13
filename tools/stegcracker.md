# stegcracker — steghide brute-force wrapper

> **Dictionary attack against steghide-embedded files.** Just wraps `steghide extract` in a loop with rockyou.

**Install check:** `stegcracker --version` (`pip install stegcracker`)

---

## 🎯 Cheat-flow

```bash
stegcracker photo.jpg                                     # uses rockyou.txt
stegcracker photo.jpg /usr/share/wordlists/fasttrack.txt  # custom list
stegcracker photo.jpg rockyou.txt -o out.txt              # output
```

On success:
```
Successfully cracked file with password: "hunter2"
Tried 1337 passwords
Your file has been written to: photo.jpg.out
```

---

## 🔑 Args

| Arg | Meaning |
|---|---|
| `<file>` | Stego file |
| `<wordlist>` | Password wordlist (defaults to rockyou on Parrot/Kali) |
| `-o <file>` | Override output filename |
| `-c` | Don't quit on first hit (continue scanning) |
| `-t <N>` | Threads (newer versions) |

---

## 📋 Workflow for exam-style stego questions

```bash
# 1. Identify file type
file image.jpg

# 2. Check metadata first (often has the password/hint)
exiftool image.jpg

# 3. Check for steghide embeds
stegcracker image.jpg /usr/share/wordlists/rockyou.txt

# 4. Extract manually once you have the password
steghide extract -sf image.jpg -p hunter2

# 5. Read extracted content
cat image.jpg.out
```

---

## ⚠️ Gotchas

- **Only works against steghide.** For other steganographers try:
  - `stegoveritas image.png` — auto-try many tools
  - `zsteg image.png` — PNG/BMP LSB
  - `stegseek file.jpg rockyou.txt` — **much faster** replacement for stegcracker (hash table of passwords)
- rockyou must be gunzipped: `gunzip /usr/share/wordlists/rockyou.txt.gz`.
- Not every image contains stego — check `file`, `binwalk`, and `exiftool` first.

---

## 🔗 Related

- [steghide](steghide.md) · [exiftool](exiftool.md) · [binwalk](binwalk.md) · [foremost](foremost.md) · [strings](strings.md)
