# stegseek — Ultra-fast Steghide Cracker

> "Cracks steghide passphrases at ~1000x speed of manual brute force. If steghide asks for a password you dont know — stegseek is the answer."

**Install check (Parrot):** `stegseek --help` or download from GitHub releases

---

## 🎯 Cheat-flow

| You need to... | Command |
|---|---|
| Crack passphrase with rockyou | `stegseek steg.jpg /usr/share/wordlists/rockyou.txt` |
| Crack with custom wordlist | `stegseek steg.jpg custom_words.txt` |
| Seed crack (no wordlist) | `stegseek --seed steg.jpg` |
| Specify output file | `stegseek steg.jpg wordlist.txt -xf output.txt` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-sf <file>` | Stego file (can also be positional arg) |
| `-wl <file>` | Wordlist file (can also be positional arg) |
| `-xf <file>` | Output extracted data to specific file |
| `-f` | Force overwrite output |
| `--seed` | Seed-based crack (no wordlist needed) |
| `-t <n>` | Number of threads |

---

## 📋 Recipes

```bash
# 1. Standard crack with rockyou (MOST COMMON)
stegseek image.jpg /usr/share/wordlists/rockyou.txt

# 2. If rockyou is compressed
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
stegseek image.jpg /usr/share/wordlists/rockyou.txt

# 3. Crack and save output
stegseek image.jpg /usr/share/wordlists/rockyou.txt -xf secret.txt

# 4. Seed-based (tries patterns without wordlist)
stegseek --seed image.jpg

# 5. Custom wordlist
stegseek image.jpg /tmp/passwords.txt

# 6. Full pipeline: crack → read
stegseek photo.jpg /usr/share/wordlists/rockyou.txt -xf flag.txt && cat flag.txt
```

---

## 💡 Exam Tips

- stegseek cracks rockyou.txt in **seconds** — dont waste time manually guessing
- Default output file is `<input>.out` (e.g., `image.jpg.out`)
- Only works on **steghide-compatible files** — JPEG, BMP, WAV, AU
- Try stegseek BEFORE manual passphrase guessing
- If stegseek finds nothing with rockyou → password might be in the question text or EXIF data

---

## ⚠️ Gotchas

- Not pre-installed on all distros — may need manual install
- Only cracks **steghide** passphrases — wont work on OpenStego, Snow, or zsteg files
- Needs the same file format steghide supports (JPEG/BMP/WAV/AU)
- rockyou.txt might be gzipped — decompress first

---

## 🔗 Related

- [steghide.md](steghide.md) — the tool stegseek cracks
- [john.md](john.md) — offline hash cracking (different purpose)
