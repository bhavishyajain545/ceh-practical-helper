# base64 & xxd — Encoding/Decoding Utilities

> "Simple but essential. Almost every crypto question involves base64 or hex conversion somewhere."

**Pre-installed on all Linux systems.**

---

## base64

| You need to... | Command |
|---|---|
| Encode string | `echo -n "text" \| base64` |
| Decode string | `echo "dGV4dA==" \| base64 -d` |
| Encode file | `base64 file.bin > encoded.txt` |
| Decode file | `base64 -d encoded.txt > decoded.bin` |

### Key flags
- `-d` = decode
- `-w 0` = no line wrapping (single line output)
- Always use `echo -n` (no newline) when encoding strings

---

## xxd — Hex Dump & Conversion

| You need to... | Command |
|---|---|
| Hex dump of file | `xxd file.bin` |
| Hex dump first 32 bytes | `xxd -l 32 file.bin` |
| Hex string to binary | `echo "48656c6c6f" \| xxd -r -p` |
| Binary to hex string | `xxd -p file.bin` |
| Edit hex (create file from hex) | `echo "89504e47" \| xxd -r -p > header.bin` |

### Key flags
- `-r` = reverse (hex to binary)
- `-p` = plain hex (no formatting)
- `-l N` = limit to N bytes
- `-s +N` = skip N bytes

---

## 📋 Common Exam Recipes

```bash
# Decode base64 string
echo "Q0VIe2ZsYWdfZm91bmR9" | base64 -d
# Output: CEH{flag_found}

# Hex to ASCII
echo "4345487b666c61677d" | xxd -r -p
# Output: CEH{flag}

# Check file magic bytes
xxd file.dat | head -1
# 89504e47 = PNG, 25504446 = PDF, ffd8ffe0 = JPEG

# Multi-layer decode
echo "encoded" | base64 -d | xxd -r -p

# Encode a message for embedding
echo -n "secret" | base64
# Output: c2VjcmV0
```

---

## 🔗 Related

- [openssl.md](openssl.md) — more advanced crypto operations
- [hash-identifier.md](hash-identifier.md) — identify hash types
