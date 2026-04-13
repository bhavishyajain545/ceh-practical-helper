# Scenario 09 — Steganography Image to VeraCrypt Container

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 18 Cryptography, 06 Malware Analysis (forensics)
**Tools:** [exiftool](../../tools/exiftool.md), [steghide](../../tools/steghide.md), [binwalk](../../tools/binwalk.md), [foremost](../../tools/foremost.md), `veracrypt`

## Story
You're given `holiday.jpg` and a file `vault.hc`. Extract the flag hidden inside the VeraCrypt container.

## Step 1 — Image metadata
```bash
exiftool holiday.jpg
```
**Expected finding:** a suspicious `Comment` or `Artist` field with a hint (e.g. "password is my dog's name + year").
**→ Tool ref:** [exiftool](../../tools/exiftool.md)

## Step 2 — Check for embedded data
```bash
binwalk holiday.jpg
binwalk -e holiday.jpg
file holiday.jpg
```
**Expected finding:** possibly a zip/appended data. If nothing, move on.
**→ Tool ref:** [binwalk](../../tools/binwalk.md)

## Step 3 — Steghide extract
```bash
steghide info holiday.jpg
steghide extract -sf holiday.jpg -p ''
# if empty pass fails, brute:
stegseek holiday.jpg /usr/share/wordlists/rockyou.txt
```
**Expected finding:** `secret.txt` containing the VeraCrypt password.
**→ Tool ref:** [steghide](../../tools/steghide.md)

## Step 4 — Mount VeraCrypt container
```bash
veracrypt --text --mount vault.hc /mnt/vault \
          --password "$(cat secret.txt)" --pim=0 --keyfiles="" --protect-hidden=no
ls /mnt/vault
```
**Expected finding:** `flag.txt` inside.

## Step 5 — Read flag
```bash
cat /mnt/vault/flag.txt
veracrypt --text --dismount /mnt/vault
```

## Step 6 — Final answer
**Answer format:** the flag string.

## Gotchas across this chain
- `steghide` only works on JPEG/BMP/WAV/AU. For PNG, use `zsteg` or `stegsolve`.
- `stegseek` is 1000x faster than `steghide` brute — use it if installed.
- VeraCrypt CLI needs explicit `--pim=0 --keyfiles="" --protect-hidden=no` or it hangs on TTY prompts.
- Common stego passphrases: empty, `password`, filename without extension, a string from EXIF.
- `binwalk -e` dumps to `_holiday.jpg.extracted/` — look there, not next to the original.

## Variant questions this scenario teaches you to handle
- "Extract hidden data from image.jpg" → steps 2–3.
- "What is the EXIF comment of this image?" → step 1.
- "Mount this VeraCrypt container" → step 4.
- "Find the flag in the forensic image" → full chain.
