# binwalk — firmware/file carving & entropy analysis

> **The "what's hidden inside this file?" tool.** Finds embedded files (zips inside JPEGs, ELFs inside firmware blobs, etc.) via signature scanning. Pairs with [exiftool](./exiftool.md) and [steghide](./steghide.md) for any image-analysis Q.

**Install check (Parrot — already installed):** `binwalk --help | head`
**Install if missing:** `sudo apt install binwalk`

---

## 🎯 Cheat-flow: "What's in this file?"

| Step | Command | Why |
|---|---|---|
| 1. Signature scan | `binwalk <file>` | Shows embedded file signatures |
| 2. Extract everything | `binwalk -e <file>` | Auto-carves into `_<file>.extracted/` |
| 3. Recursive extract | `binwalk -Me <file>` | Follow embedded archives too |
| 4. Entropy analysis | `binwalk -E <file>` | High entropy = encryption/compression |
| 5. Raw carve at offset | `binwalk --dd='.*' <file>` | Dump every signature match |
| 6. Hex view around offset | `binwalk -W <file>` or `xxd` | Manual inspection |

---

## 🔑 Flags you must know cold

### Scan modes
| Flag | Meaning |
|---|---|
| `<file>` | Default = signature scan |
| `-B` / `--signature` | Explicit signature scan |
| `-E` / `--entropy` | Entropy analysis (spots encrypted blobs) |
| `-A` / `--opcodes` | Scan for executable opcodes (ARM, x86, MIPS…) |
| `-Y` / `--disasm` | Capstone disassembly fingerprinting |
| `-H` / `--hexdump` | Hexdump + diff two files |
| `-W` | Diff/hexdump view |

### Extraction
| Flag | Meaning |
|---|---|
| `-e` / `--extract` | Extract using default signatures |
| `-M` / `--matryoshka` | **Recursive** extraction |
| `-r` / `--rm` | Remove files that failed to extract |
| `-d <n>` / `--depth=<n>` | Max recursion depth |
| `-D <type:ext:cmd>` / `--dd=<...>` | Custom carve rule |
| `--dd='.*'` | Carve **every** signature hit |
| `-C <dir>` / `--directory=<dir>` | Output dir |
| `-j <n>` / `--size=<n>` | Max bytes to scan |
| `-o <n>` / `--offset=<n>` | Start offset |

### Output / filtering
| Flag | Meaning |
|---|---|
| `-q` | Quiet |
| `-v` | Verbose |
| `-l <file>` | Log to file |
| `-f <file>` | Log to file (alt) |
| `-y <type>` | Only show signatures matching type (e.g. `-y jpeg`) |
| `-x <type>` | Exclude a type |

---

## 📋 Command recipes (copy-paste)

### Signature scan (always run first)
```bash
binwalk suspicious.jpg

# Sample output:
# DECIMAL    HEXADECIMAL    DESCRIPTION
# ----------------------------------------------------
# 0          0x0            JPEG image data
# 45721      0xB299         Zip archive data, name: flag.txt
# 45983      0xB39F         End of Zip archive
```

### Extract embedded files
```bash
# Extract known signatures into ./_suspicious.jpg.extracted/
binwalk -e suspicious.jpg

# Recursive — keep carving inside extracted files
binwalk -Me firmware.bin

# Extract to a specific directory
binwalk -e -C /tmp/carve suspicious.jpg

# Extract AND remove failed extractions
binwalk -er firmware.bin
```

### Custom carve — dump everything found as raw blobs
```bash
# Dump every signature hit as its own file
binwalk --dd='.*' suspicious.jpg

# Only carve JPEGs out
binwalk --dd='jpeg:jpg' suspicious.bin

# Only carve zip archives
binwalk --dd='zip archive:zip' firmware.bin
```

### Entropy analysis (spot encrypted / compressed regions)
```bash
# Entropy plot — rising to ~1.0 = encrypted or compressed
binwalk -E firmware.bin

# Entropy + signature in one pass
binwalk -EB firmware.bin

# Save entropy plot to PNG (needs matplotlib / pyqtgraph)
binwalk -E --save firmware.bin
```

### Opcode scan (find executable code sections)
```bash
binwalk -A firmware.bin
```

### The image-analysis combo
```bash
# 1. Metadata
exiftool suspicious.jpg

# 2. Appended / embedded files
binwalk suspicious.jpg
binwalk -e suspicious.jpg

# 3. Stego LSB payload
steghide extract -sf suspicious.jpg -p ""

# 4. Raw strings scan
strings -n 8 suspicious.jpg | less
```

### Manual carve with dd (when binwalk's extractor fails)
```bash
# Say binwalk shows a zip at offset 45721
dd if=suspicious.jpg of=hidden.zip bs=1 skip=45721
unzip hidden.zip
```

---

## ⚠️ Gotchas

- **"Nothing found" doesn't mean nothing's there.** Binwalk only detects **known signatures**. Also run `strings`, `exiftool`, and `steghide`.
- **Extraction dir is `_<filename>.extracted/`** with a leading underscore. `ls -la` if you don't see it.
- **Recursive (`-Me`) can explode in size** on real firmware — use `-d 2` to cap depth.
- **Entropy ≈ 1.0 throughout** = already encrypted/compressed (e.g. packed firmware). Can't carve until decrypted.
- **False positives are common.** Binwalk flags anything that looks like a magic byte — not everything labeled "Zip archive" actually is one. Verify with `file` / `unzip -t`.
- **`binwalk -e` silently skips signatures without an extractor** (no binutils/sasquatch installed). Install `sudo apt install binwalk-extras sasquatch firmware-mod-kit` if you need JFFS2/SquashFS.
- **Running as root** lets binwalk preserve permissions on extracted files — sometimes needed for nested extractions.
- **Always cross-check with `foremost`** for file recovery from disk images — different signature DB.

---

## 🔗 Related

- [foremost — file recovery from disk / pcap](./foremost.md)
- [exiftool — metadata first](./exiftool.md)
- [steghide — LSB stego in JPEG/WAV](./steghide.md)
- [Forensics question bank](../questions/forensics.md)
