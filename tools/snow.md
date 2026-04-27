# snow — Whitespace Steganography

> "Hides messages in the whitespace (spaces & tabs) at the end of text lines. Exam mein text file stego question aaye toh Snow yaad karo."

**Install check (Parrot):** `snow --help` or `sudo apt install stegsnow`

> ⚠️ Some distros install it as `stegsnow` instead of `snow`.

---

## 🎯 Cheat-flow

| You need to... | Command |
|---|---|
| Extract hidden message (no password) | `snow -C steg_file.txt` |
| Extract with password | `snow -C -p "password" steg_file.txt` |
| Hide a message (no password) | `snow -C -m "secret message" cover.txt out.txt` |
| Hide with password | `snow -C -m "secret" -p "pass" cover.txt out.txt` |
| Check if file has trailing whitespace | `cat -A file.txt` (look for trailing spaces/tabs) |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-C` | Compress data (always use this) |
| `-m "msg"` | Message to hide |
| `-p "pass"` | Password/passphrase |
| `-f msgfile` | Read message from file instead of `-m` |
| `-l linelen` | Max line length (default 80) |
| `-Q` | Quiet mode |

---

## 📋 Recipes

```bash
# 1. Extract hidden message (most common exam scenario)
snow -C secret_doc.txt

# 2. Extract with password
snow -C -p "mysecret" secret_doc.txt

# 3. Hide message in text file
snow -C -m "CEH{hidden_flag}" -p "exampass" cover.txt steg_output.txt

# 4. Hide contents of a file
snow -C -f flag.txt -p "pass123" cover.txt steg_output.txt

# 5. Detect whitespace stego (look for trailing spaces)
cat -A suspicious.txt | head -10
# If you see lots of trailing spaces/tabs at line ends → Snow likely used

# 6. Hex dump to spot trailing whitespace
xxd suspicious.txt | grep "2020\|0920"
```

---

## 💡 Exam Tips

- Snow ONLY works on **text files** — not images, not binaries
- Look for files that seem **normal text but suspiciously large** for their content
- Password is often hidden in the question or in EXIF metadata of another file
- Try **empty password first** (just press Enter or omit `-p`)
- If `snow` not found, try `stegsnow`
- The `-C` flag (compress) is needed for both embed AND extract

---

## ⚠️ Gotchas

- Without `-C`, extraction may give garbage — **always use `-C`**
- Some editors strip trailing whitespace on save — **don't edit the steg file**
- Windows line endings (`\r\n`) can break extraction — use `dos2unix` first
- Snow cannot handle very long messages — limited by number of lines in cover file

---

## 🔗 Related

- [steghide.md](steghide.md) — JPEG/WAV stego
- [exiftool.md](exiftool.md) — metadata (password might be here)
- [strings.md](strings.md) — quick check for readable text
