# zsteg — PNG/BMP steganography detector

> **Detects LSB steganography** and other hidden data in PNG/BMP images. Complements `steghide` (JPEG/BMP) and `binwalk` (generic file carving). CEH: "find the flag hidden in this PNG".

**Install check (Parrot):** `zsteg --version` (install: `sudo gem install zsteg`).

Ruby-based. If `gem` missing: `sudo apt install ruby-full` first.

---

## 🎯 Cheat-flow

| Goal | Command |
|---|---|
| **Default scan** (most common stego) | `zsteg image.png` |
| All bitplanes, all channels | `zsteg -a image.png` |
| Extract specific payload | `zsteg -E 'b1,rgb,lsb,xy' image.png > out.bin` |
| Skip noisy false positives | `zsteg image.png \| grep -vE "(ascii|utf8)"` |
| Verbose detection | `zsteg -v image.png` |
| Check BMP | `zsteg image.bmp` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-a` / `--all` | Try all bitplanes, orders, channels |
| `-E <spec>` | Extract payload matching spec |
| `-v` | Verbose |
| `-l <len>` | Limit output length |
| `--lsb` / `--msb` | Bit order |
| `--invert` | Invert bits |

**Payload spec syntax:** `b<N>,<channels>,<order>,<xy|yx>`
- `b1` = LSB, `b2` = 2nd bit, … `b8` = MSB
- channels: `r`, `g`, `b`, `a`, `rgb`, `bgr`
- order: `lsb` or `msb`
- orientation: `xy` (row-major) or `yx` (column-major)

Example: `b1,rgb,lsb,xy` = LSB of RGB, row-by-row — the classic stego.

---

## ⚡ CEH workflow

```bash
# Step 1: quick check
zsteg image.png

# Step 2: if nothing obvious, try all
zsteg -a image.png | head -40

# Step 3: extract the interesting hit
zsteg -E 'b1,rgb,lsb,xy' image.png > payload.bin
file payload.bin      # check what it is
strings payload.bin   # or run binwalk if it's a file
```

---

## ⚠️ Gotchas

- **Only PNG + BMP.** For JPEG → use `steghide` / `stegoveritas` / `jsteg`.
- Produces MANY false positives — read output critically.
- Flag-like strings in output aren't always real — verify (decode base64, check format).
- Large images = slow with `-a`; crop first if needed.
- Sometimes the flag is in multiple extracted blobs — try top 3-5 hits.
- Installation via gem can fail without `ruby-dev` — `sudo apt install ruby-dev` if `gem install` errors.

---

## 🧠 When to use zsteg vs alternatives

| File type | Tool |
|---|---|
| PNG, BMP | **zsteg** |
| JPG | `steghide`, `stegoveritas`, `jsteg` |
| WAV, audio | `audacity` (spectrogram), `sonic-visualiser` |
| Generic file carving | `binwalk`, `foremost` |
| Metadata | `exiftool` |
| All-in-one online | [Aperi'Solve](https://www.aperisolve.com/) |

---

## 📖 See also
- [steghide.md](steghide.md)
- [binwalk.md](binwalk.md)
- [exiftool.md](exiftool.md)
- [online-tools/stego-tools.md](../online-tools/stego-tools.md)
