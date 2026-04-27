# 🗂️ FILE MILI — KAUNSA TOOL USE KARU?

---

## Image Files
| Extension | Tool | Command |
|---|---|---|
| `.jpg` / `.jpeg` | steghide | `steghide extract -sf file.jpg` |
| `.jpg` (pass unknown) | stegseek | `stegseek file.jpg rockyou.txt` |
| `.png` | zsteg | `zsteg file.png` |
| `.bmp` | zsteg / steghide | `zsteg file.bmp` |
| Any image | exiftool | `exiftool file` (metadata) |
| Any image | binwalk | `binwalk -e file` (embedded) |
| Any image | strings | `strings file \| grep flag` |
| Any image | stegsolve | `java -jar stegsolve.jar` (visual) |

## Audio Files
| Extension | Tool | Command |
|---|---|---|
| `.wav` / `.au` | steghide | `steghide extract -sf file.wav` |
| `.mp3` | strings/binwalk | `strings file.mp3 \| grep flag` |

## Text Files
| Extension | Tool | Command |
|---|---|---|
| `.txt` (whitespace) | snow | `snow -C file.txt` |
| `.txt` (encoded) | base64 | `base64 -d file.txt` |

## Encrypted Files
| Extension | Tool | Command |
|---|---|---|
| `.enc` | openssl | `openssl enc -aes-256-cbc -d -in file.enc` |
| `.gpg` | gpg | `gpg -d file.gpg` |
| `.hc` | veracrypt | `veracrypt --text file.hc /mnt/vc` |

## Archives (Password Protected)
| Extension | Tool | Command |
|---|---|---|
| `.zip` | zip2john → john | `zip2john f.zip > h.txt && john h.txt` |
| `.rar` | rar2john → john | `rar2john f.rar > h.txt && john h.txt` |
| `.7z` | 7z2john → john | `7z2john.pl f.7z > h.txt && john h.txt` |
| `.pdf` | pdf2john → john | `pdf2john.pl f.pdf > h.txt && john h.txt` |

## Network Files
| Extension | Tool | Command |
|---|---|---|
| `.pcap` / `.pcapng` | wireshark / tshark | `wireshark file.pcap` |
| `.cap` (wifi) | aircrack-ng | `aircrack-ng -w rockyou.txt file.cap` |

## Android
| Extension | Tool | Command |
|---|---|---|
| `.apk` | apktool | `apktool d file.apk` |
| `.apk` (source) | jadx | `jadx -d out/ file.apk` |
| `.db` (sqlite) | sqlite3 | `sqlite3 file.db ".tables"` |

## Binary / Unknown
| Check | Tool | Command |
|---|---|---|
| File type | file | `file unknown_file` |
| Magic bytes | xxd | `xxd unknown_file \| head -1` |
| Hidden strings | strings | `strings file \| grep -i flag` |
| Embedded files | binwalk | `binwalk -e file` |

---

## UNIVERSAL FIRST STEPS (koi bhi file mile):
```bash
file mystery_file                          # 1. Type identify
exiftool mystery_file                      # 2. Metadata check
strings mystery_file | grep -i flag        # 3. Quick string search
binwalk mystery_file                       # 4. Embedded files
xxd mystery_file | head -3                 # 5. Magic bytes
```
