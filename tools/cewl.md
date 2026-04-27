# cewl — Custom Wordlist Generator from Websites

> "Spiders a target website and builds a wordlist from the words found. Use when you need a targeted wordlist specific to the target organization."

**Install check:** `cewl --help` (pre-installed on Parrot/Kali)

---

## 🎯 Cheat-flow

| You need to... | Command |
|---|---|
| Basic wordlist from site | `cewl http://target.com -w wordlist.txt` |
| Min word length 6 | `cewl http://target.com -m 6 -w wordlist.txt` |
| Spider depth 3 | `cewl http://target.com -d 3 -w wordlist.txt` |
| Include emails found | `cewl http://target.com -e --email_file emails.txt` |
| With authentication | `cewl http://target.com --auth_type basic --auth_user admin --auth_pass password` |
| Lowercase all words | `cewl http://target.com --lowercase -w wordlist.txt` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-w <file>` | Write output to file |
| `-m <n>` | Minimum word length (default: 3) |
| `-d <n>` | Spider depth (default: 2) |
| `-e` | Extract emails |
| `--email_file <file>` | Save emails to file |
| `--lowercase` | Convert all to lowercase |
| `-c` | Show word count |
| `-n` | Dont output words to stdout |
| `--with-numbers` | Include words with numbers |
| `-a` | Include metadata from files |
| `--auth_type` | Auth type: basic, digest, ntlm |
| `--auth_user` | Username for auth |
| `--auth_pass` | Password for auth |
| `--proxy_host` | Proxy host |
| `-v` | Verbose |

---

## 📋 Recipes

```bash
# 1. Basic wordlist (most common exam usage)
cewl http://192.168.52.129 -w target_words.txt
wc -l target_words.txt

# 2. Targeted: min 6 chars, depth 3
cewl http://192.168.52.129 -m 6 -d 3 -w custom.txt

# 3. Get emails too
cewl http://192.168.52.129 -e --email_file emails.txt -w words.txt

# 4. Include words with numbers
cewl http://192.168.52.129 --with-numbers -w words_nums.txt

# 5. Lowercase + min 8 chars (password-like)
cewl http://192.168.52.129 --lowercase -m 8 -w passwords.txt

# 6. Pipe directly to hydra
cewl http://192.168.52.129 -m 6 -w - | hydra -l admin -P - 192.168.52.129 ssh -t 4

# 7. Spider DVWA specifically
cewl http://192.168.52.129/dvwa/ -d 2 -m 5 -w dvwa_words.txt

# 8. With basic auth
cewl http://192.168.52.129/restricted/ --auth_type basic --auth_user admin --auth_pass password -w words.txt
```

---

## 💡 Exam Tips

- Use CeWL when **generic wordlists fail** — target-specific words often work
- Great for organizations with **custom passwords** based on company name, products, etc.
- Combine with **crunch** for mutations: CeWL gives base words, crunch adds numbers/symbols
- Pipe output directly to **hydra** to save time
- `-m 6` is a good default — passwords under 6 chars are rare
- Spider depth 2-3 is usually enough — higher = slower

---

## ⚠️ Gotchas

- Only finds words on **web pages** — wont find words in images or JavaScript
- Depth > 3 can be very slow on large sites
- Some sites need auth — use `--auth_*` flags
- Output may have duplicates — pipe through `sort -u` if needed
- HTTPS sites may need `--proxy` or ignore cert errors

---

## 🔗 Related

- [crunch.md](crunch.md) — pattern-based wordlist generation
- [hydra.md](hydra.md) — use CeWL output for brute force
- [john.md](john.md) — use CeWL words as base for rules
