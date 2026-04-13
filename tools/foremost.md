# foremost — file recovery / carving

> **The "recover deleted files" tool.** Carves files out of disk images, memory dumps, and pcaps based on headers/footers. If the exam gives you a `.dd`, `.img`, `.raw`, or pcap and asks "what files were transferred / deleted / stored", run foremost.

**Install check (Parrot — already installed):** `foremost -V`
**Install if missing:** `sudo apt install foremost`

---

## 🎯 Cheat-flow: "Recover files from this image"

| You have... | Run this |
|---|---|
| Raw disk image (`.dd`, `.img`, `.raw`) | `foremost -i image.dd -o out/` |
| Specific file types only | `foremost -t jpg,pdf,doc -i image.dd -o out/` |
| Pcap with transferred files | `foremost -i capture.pcap -o out/` |
| Custom config | `foremost -c myconf.conf -i image.dd -o out/` |
| Verbose + quick mode | `foremost -v -Q -i image.dd -o out/` |

Output lands in `out/<filetype>/` plus an `audit.txt` summary.

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-i <file>` | **Input** file (disk image, pcap, raw blob) |
| `-o <dir>` | **Output** directory (must not already exist) |
| `-t <types>` | Comma-sep file types to carve |
| `-T` | Timestamp the output dir (avoids "exists" error) |
| `-c <conf>` | Custom config file (default `/etc/foremost.conf`) |
| `-v` | Verbose |
| `-Q` | Quiet mode + quick (skip header-less carves) |
| `-q` | Quick mode only |
| `-w` | Write audit file only, don't extract |
| `-a` | Write all headers, no error detection |
| `-b <n>` | Block size (default 512) |
| `-k <n>` | Chunk size in MB |
| `-s <n>` | Skip first n blocks |

### Supported file types (`-t` values)
`jpg`, `gif`, `png`, `bmp`, `avi`, `exe`, `mpg`, `mov`, `wav`, `riff`, `wmv`, `mp4`, `ole` (Office ≤2003), `doc`, `zip`, `rar`, `htm`, `cpp`, `pdf`, `all`

---

## 📋 Command recipes (copy-paste)

### Carve everything out of a disk image
```bash
foremost -i disk.dd -o recovered/
ls recovered/
# → audit.txt  jpg/  pdf/  zip/  ...
cat recovered/audit.txt
```

### Only hunt for specific types
```bash
# Images
foremost -t jpg,png,gif,bmp -i disk.dd -o recovered/

# Docs
foremost -t pdf,doc,docx,ole -i disk.dd -o recovered/

# Everything foremost knows about
foremost -t all -i disk.dd -o recovered/
```

### Carve files from a pcap (common exam scenario)
```bash
foremost -i capture.pcap -o pcap-files/
ls pcap-files/
cat pcap-files/audit.txt
```

### Output dir already exists? Timestamp it
```bash
foremost -T -t all -i disk.dd -o recovered
# creates recovered_Tue_Apr_14_12_30_00_2026/
```

### Verbose + quick
```bash
foremost -v -Q -t all -i memory.raw -o out/
```

### Scan without extracting (audit only)
```bash
foremost -w -i disk.dd -o audit-only/
cat audit-only/audit.txt
```

### Read the audit file
```bash
cat recovered/audit.txt
# Shows: file#, name, size, offset, comment
```

---

## 🧪 binwalk vs foremost — which to use?

| Scenario | Use |
|---|---|
| Disk image (`.dd`, `.img`) | **foremost** |
| Memory dump | **foremost** (then `volatility`) |
| PCAP file transfers | **foremost** (or `tshark --export-objects`) |
| Single suspicious image/binary | **binwalk** |
| Firmware blob | **binwalk** |
| Nested/recursive extraction | **binwalk -Me** |
| Unknown? Run both. | both |

---

## ⚠️ Gotchas

- **Output dir must not exist** (or use `-T` to timestamp). `mkdir out` then `-o out` will fail.
- **Run as root / sudo** for raw device files (`/dev/sdb`), not needed for image files.
- **`audit.txt` is the map.** Always read it — it lists every carved file, size, offset, and any errors.
- **Carved files may be corrupt** (file carved mid-write, fragmented on disk). Check each one with `file <name>`.
- **Foremost can't defragment.** Files split across non-contiguous blocks won't reassemble. Use `scalpel` or `photorec` as alternatives if foremost misses something.
- **`-t all` uses default config.** If a type isn't in `/etc/foremost.conf`, it won't carve. Edit the conf or add signatures.
- **Pcap carving = files transferred in cleartext.** Anything over TLS won't recover.
- **Disk image format matters:** foremost wants **raw** (`dd`, `.img`, `.raw`). For E01/EWF, first `ewfmount` or convert with `ewfexport`. For VMDK, mount with `qemu-nbd`.
- **Always verify recovered files** — open them, check hashes, diff vs expected.

---

## 🔗 Related

- [binwalk — firmware & single-file carving](./binwalk.md)
- [exiftool — check metadata of recovered files](./exiftool.md)
- [Forensics question bank](../questions/by-domain/07-sniffing.md)
- [Forensics playbook](../playbooks/forensics-playbook.md)
