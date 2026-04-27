# John Companion Tools — *2john Hash Extractors

> "Before you can crack a password-protected file with John, you need to extract its hash. These tools do that."

All tools are part of the **john** package — pre-installed on Parrot/Kali.

---

## 🎯 Master Cheat Table

| Protected File | Extract Hash With | Then Crack With |
|---|---|---|
| ZIP archive | `zip2john file.zip > hash.txt` | `john hash.txt` |
| RAR archive | `rar2john file.rar > hash.txt` | `john hash.txt` |
| PDF file | `pdf2john.pl file.pdf > hash.txt` | `john hash.txt` |
| 7z archive | `7z2john.pl file.7z > hash.txt` | `john hash.txt` |
| Office doc | `office2john.py file.docx > hash.txt` | `john hash.txt` |
| SSH private key | `ssh2john.py id_rsa > hash.txt` | `john hash.txt` |
| KeePass DB | `keepass2john file.kdbx > hash.txt` | `john hash.txt` |
| Linux shadow | `unshadow passwd shadow > hash.txt` | `john hash.txt` |
| BitLocker | `bitlocker2john -i disk.img > hash.txt` | `john hash.txt` |
| TrueCrypt/VeraCrypt | `truecrypt2john volume > hash.txt` | `john hash.txt` |
| WiFi handshake | `wpapcap2john capture.cap > hash.txt` | `john hash.txt` |

---

## 📋 Most Common Recipes

### zip2john — Crack ZIP passwords
```bash
zip2john secret.zip > zip_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
john --show zip_hash.txt
# Then unzip
unzip -P <cracked_password> secret.zip
```

### rar2john — Crack RAR passwords
```bash
rar2john archive.rar > rar_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt
john --show rar_hash.txt
unrar x -p<password> archive.rar
```

### pdf2john — Crack PDF passwords
```bash
pdf2john.pl confidential.pdf > pdf_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt pdf_hash.txt
john --show pdf_hash.txt
```

### unshadow — Combine passwd + shadow for John
```bash
# Get both files from target
cat /etc/passwd > passwd.txt
cat /etc/shadow > shadow.txt
# Combine
unshadow passwd.txt shadow.txt > combined.txt
# Crack
john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
john --show combined.txt
```

### ssh2john — Crack SSH key passphrase
```bash
ssh2john.py id_rsa > ssh_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt ssh_hash.txt
john --show ssh_hash.txt
```

---

## 💡 Exam Tips

- **Always extract hash first**, then crack — never try to brute force the file directly
- Hash extraction is instant — cracking takes time
- Some tools are `.pl` (Perl) or `.py` (Python) scripts — may need `perl` or `python3` prefix
- Location: `/usr/share/john/` or `/opt/john/` depending on install
- After cracking, use `john --show hash.txt` to display results
- `--wordlist=/usr/share/wordlists/rockyou.txt` is your default wordlist

---

## ⚠️ Gotchas

- Script names vary: `pdf2john` vs `pdf2john.pl` vs `pdf2john.py`
- If not in PATH: `locate pdf2john` to find it
- `unshadow` needs BOTH passwd AND shadow files
- Some *2john scripts need Python 2 — try `python2` if `python3` fails
- rockyou.txt might be gzipped: `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`

---

## 🔗 Related

- [john.md](john.md) — the cracker itself
- [hashcat.md](hashcat.md) — GPU alternative for cracking
