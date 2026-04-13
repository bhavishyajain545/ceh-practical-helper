# strings — extract printable strings from binary

> **First tool on any unknown blob.** Pulls ASCII/Unicode sequences out of binaries, memory dumps, PCAPs, malware.

**Install check:** `strings --version` (binutils)

---

## 🎯 Cheat-flow

```bash
strings file.exe                      # ASCII strings ≥ 4 chars
strings -n 8 file.exe                 # minimum length 8
strings -e l file.exe                 # UTF-16 little-endian (Windows)
strings -a file.bin                   # scan whole file, not just data sections
strings -t x file.exe | head          # show hex offset
strings file.exe | grep -i "http\|key\|pass"
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-n <N>` | Minimum string length (default 4) |
| `-a` / `--all` | Scan entire file |
| `-t d|o|x` | Prefix with offset (dec/oct/hex) |
| `-e s|S|b|l|B|L` | Encoding: 7-bit, 8-bit, 16-bit big/little, 32-bit big/little |
| `-f` | Show filename on each line (multi-file) |
| `-w` | Don't require whitespace to terminate strings |

Encodings you care about:
- `-e s` — ASCII (default)
- `-e l` — UTF-16 LE (Windows PE .rdata)
- `-e b` — UTF-16 BE

---

## 📋 Recipes

```bash
# 1. Hunt URLs / creds in a binary
strings -a -n 6 sample.exe | grep -iE "http|\.com|key|password|token"

# 2. Windows exe — combine ASCII + UTF-16
(strings -a sample.exe; strings -a -e l sample.exe) | sort -u > all.txt

# 3. See what language/runtime
strings sample.exe | grep -iE "python|java|golang|\.NET|mscoree"

# 4. PCAP extraction
strings -n 8 capture.pcap | grep -i "USER\|PASS\|http"

# 5. Memory dump triage
strings -a -t x lsass.dmp | grep -i "password"
```

---

## ⚠️ Gotchas

- Default `-n 4` misses 1–3 char tokens — but lowering gets noisy.
- Default scans only **loadable data sections**; use `-a` for the whole file.
- Windows binaries hold strings in UTF-16 → always add `-e l` (or BusyBox `strings` won't have it).
- strings is a **first-look** tool — use [file](file.md), [binwalk](binwalk.md), [peframe](peframe.md) for real analysis.

---

## 🔗 Related

- [file](file.md) · [binwalk](binwalk.md) · [exiftool](exiftool.md) · [foremost](foremost.md) · [peframe](peframe.md)
