# crunch — Custom Wordlist Generator

> "Generate targeted wordlists when rockyou is too big or you know the password pattern. Exam mein custom brute force ke liye."

**Install check:** `crunch` (pre-installed on Parrot/Kali)

---

## 🎯 Cheat-flow

| You need... | Command |
|---|---|
| All 4-digit PINs | `crunch 4 4 0123456789 -o pins.txt` |
| All 3-char lowercase | `crunch 3 3 -o alpha3.txt` |
| Pattern-based (Pass####) | `crunch 8 8 -t Pass%%%% -o pass_pins.txt` |
| Specific charset | `crunch 6 8 abc123 -o custom.txt` |
| Pipe directly to hydra | `crunch 4 4 0123456789 \| hydra -l admin -P - ssh://target` |

---

## 🔑 Syntax

```
crunch <min_len> <max_len> [charset] [options]
```

### Special characters for `-t` pattern

| Symbol | Represents |
|---|---|
| `@` | Lowercase letters (a-z) |
| `,` | Uppercase letters (A-Z) |
| `%` | Numbers (0-9) |
| `^` | Symbols (!@#$...) |

---

## 📋 Recipes

```bash
# 1. All 4-digit PINs (0000-9999)
crunch 4 4 0123456789 -o pins.txt
# Output: 10,000 lines

# 2. 6-8 char lowercase passwords
crunch 6 8 abcdefghijklmnopqrstuvwxyz -o alpha.txt

# 3. Pattern: company name + 2 digits
crunch 9 9 -t company%% -o company_pins.txt
# Generates: company00, company01, ... company99

# 4. Pattern: Admin + 4 digits
crunch 9 9 -t Admin%%%% -o admin_pins.txt

# 5. Pipe to hydra (no file needed)
crunch 4 4 0123456789 | hydra -l root -P - 192.168.52.129 ssh -t 4

# 6. Specific characters only
crunch 3 3 aAbB12 -o limited.txt

# 7. With max file size limit
crunch 4 6 0123456789 -b 1mb -o START
```

---

## 💡 Exam Tips

- Use crunch when you **know the password pattern** (e.g., company name + digits)
- Pipe directly to hydra with `|` to avoid creating huge files
- 4-digit PINs = 10,000 passwords — fast to crack
- Longer wordlists = HUGE files — be careful with `crunch 8 8` on full charset
- Combine with hydra: `crunch ... | hydra -l user -P - target service`

---

## ⚠️ Gotchas

- **File sizes explode quickly** — `crunch 8 8` with full charset = terabytes
- Always limit charset or use patterns for practical use
- `-t` pattern length must match min AND max length
- Pipe to hydra instead of saving to disk when possible

---

## 🔗 Related

- [hydra.md](hydra.md) — use crunch output for brute force
- [john.md](john.md) — offline cracking (different approach)
