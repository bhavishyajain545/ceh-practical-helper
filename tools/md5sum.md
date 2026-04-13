# md5sum / sha1sum / sha256sum — file hashing

> **Verify integrity, identify known files, match against VT / malware DBs.** Exam questions frequently ask "what is the md5/sha256 of file X".

**Install check:** `md5sum --version` (coreutils — always present)

---

## 🎯 Cheat-flow

```bash
md5sum file.bin                   # → <hash>  file.bin
sha1sum file.bin
sha256sum file.bin
sha256sum *                       # bulk
md5sum -b file.bin                # binary mode (Windows compat)
md5sum file > file.md5            # save for later
md5sum -c file.md5                # verify
sha256sum --ignore-missing -c sums.txt
```

---

## 🔑 Flags (same across md5sum/sha1sum/sha256sum/sha512sum)

| Flag | Meaning |
|---|---|
| `-b` | Binary mode |
| `-t` | Text mode (default on Linux) |
| `-c <file>` | Check hashes from file |
| `--quiet` | With `-c`, only print failures |
| `--status` | With `-c`, no output — exit code only |
| `--ignore-missing` | Skip missing files during `-c` |
| `--tag` | BSD-style output |

---

## 📋 Recipes

```bash
# 1. Single-answer exam question
sha256sum suspect.exe | awk '{print $1}'

# 2. Bulk hash + save
find . -type f -exec sha256sum {} \; > hashes.txt

# 3. Compare two files quickly
md5sum a b | awk '{print $1}' | sort -u   # one line = identical

# 4. Verify download
echo "abcdef...  kali.iso" | sha256sum -c

# 5. Pipe hash (stdin)
echo -n "password" | md5sum
```

---

## 🌐 Hash → VT / malware DB

Copy the SHA-256 → https://www.virustotal.com/gui/search/<hash> → no API key needed for a hash lookup. See [virustotal](virustotal.md).

---

## ⚠️ Gotchas

- `md5sum` appends a newline on stdin unless you use `echo -n`. `printf` is safer.
- MD5 is broken for collision resistance — still fine for **file identification**, not for signing.
- On Windows: `certutil -hashfile file MD5` / `Get-FileHash -Algorithm SHA256 file`.
- `-c` exits non-zero if anything fails — useful in scripts.

---

## 🔗 Related

- [virustotal](virustotal.md) · [hashid](hashid.md) · [hash-identifier](hash-identifier.md) · [file](file.md)
