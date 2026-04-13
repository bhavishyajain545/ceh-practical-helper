# file — identify file type by magic bytes

> **First question of any forensics/malware task: "what is this thing?"**

**Install check:** `file --version`

---

## 🎯 Cheat-flow

```bash
file sample                     # basic ID
file -i sample                  # MIME type
file -b sample                  # brief (no filename)
file -k sample                  # keep going — show all matches
file -z archive.tar.gz          # peek inside compressed
file -s /dev/sda1               # read special files / block devices
file *                          # bulk
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-b` | Brief (no filename prefix) |
| `-i` | MIME type (`application/x-executable; charset=binary`) |
| `-k` | Keep scanning past first match (show all) |
| `-z` | Look inside compressed files |
| `-s` | Read from block/char devices |
| `-L` | Follow symlinks |
| `-f <list>` | Read file names from list |
| `-m <magic>` | Use alternate magic file |

---

## 📋 Typical outputs

```
shell.php: PHP script, ASCII text
payload.exe: PE32+ executable (GUI) x86-64, for MS Windows
photo.jpg: JPEG image data, JFIF standard 1.01, 1920x1080
suspect.bin: ELF 64-bit LSB executable, x86-64, statically linked, stripped
data: data                         ← magic unknown; use binwalk/strings
archive: Zip archive data, at least v2.0 to extract
```

---

## 📋 Recipes

```bash
# 1. Filter only ELF binaries in a directory
file * | grep ELF | cut -d: -f1

# 2. Chained ID (file → strings → binwalk)
file unknown && strings unknown | head && binwalk unknown

# 3. Disk image quick type
file -s disk.img

# 4. MIME for scripts
file --mime-type upload.jpg    # catch disguised php
```

---

## ⚠️ Gotchas

- `file` relies on magic bytes — attackers can spoof headers. Cross-check with [binwalk](binwalk.md) / [exiftool](exiftool.md).
- Output "data" just means "unknown" — not empty.
- Extension is ignored; file looks at content.
- Upload filter bypass trick: prepend a GIF header (`GIF89a;`) to a PHP file — `file -i` will say `image/gif`. Always re-verify.

---

## 🔗 Related

- [strings](strings.md) · [binwalk](binwalk.md) · [exiftool](exiftool.md) · [foremost](foremost.md)
