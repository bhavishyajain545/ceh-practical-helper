# john — John the Ripper

> **The Swiss-army knife of offline hash cracking.** John auto-detects most formats, ships with useful `*2john` converters (zip, rar, ssh, pdf, shadow...), and has a strong rule engine. For raw GPU speed use [hashcat](./hashcat.md); for online brute forcing use [hydra](./hydra.md).

**Install check (Parrot — already installed):** `john --version` · binary path: `/usr/sbin/john` (Parrot) or `john` on PATH.

---

## 🎯 Cheat-flow: "What do I crack?"

| You need to crack... | Run this | Notes |
|---|---|---|
| A raw hash, **auto-detect** format | `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt` | Easiest |
| Force a format | `john --format=<fmt> --wordlist=rockyou.txt hash.txt` | See [formats](#common-format-names) |
| **Linux /etc/shadow** | `unshadow /etc/passwd /etc/shadow > unshadow.txt && john unshadow.txt` | Needs both files |
| **Windows NTLM** (from `secretsdump`) | `john --format=NT --wordlist=rockyou.txt hashes.txt` | Bare NT hash |
| **NTLMv2** (Responder capture) | `john --format=netntlmv2 --wordlist=rockyou.txt hash.txt` | Relay captures |
| **ZIP** archive | `zip2john secret.zip > zip.hash && john zip.hash` | Also `rar2john`, `7z2john` |
| **SSH private key** | `ssh2john id_rsa > ssh.hash && john ssh.hash` | Passphrase |
| **PDF** | `pdf2john.pl doc.pdf > pdf.hash && john pdf.hash` | |
| **KeePass** | `keepass2john file.kdbx > kp.hash && john kp.hash` | |
| Show cracked results | `john --show hash.txt` | Re-prints creds |
| Show uncracked only | `john --show=left hash.txt` | Hand these to hashcat |
| Resume a session | `john --restore` | Or `--restore=<name>` |
| Incremental brute (no wordlist) | `john --incremental hash.txt` | Slow but thorough |
| Single mode (uses GECOS) | `john --single hash.txt` | Run first, always |
| Rules on top of wordlist | `john --wordlist=rockyou.txt --rules=Jumbo hash.txt` | Mutations |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `--wordlist=<file>` | Wordlist attack (aka dictionary) |
| `--format=<name>` | Force a format (see table) |
| `--rules[=<name>]` | Enable mangling rules (`Single`, `Wordlist`, `Jumbo`, `KoreLogic`) |
| `--single` | Single crack mode (uses user/GECOS fields) |
| `--incremental[=<mode>]` | Pure brute force (`ASCII`, `Digits`, `Alpha`, `LM_ASCII`) |
| `--mask=<mask>` | Mask attack (`?l?l?l?l?d?d`) |
| `--show` | Print already-cracked hashes from `~/.john/john.pot` |
| `--show=left` | Print only still-uncracked hashes |
| `--users=<list>` | Only crack these users |
| `--groups=<list>` | Only these groups |
| `--shells=<list>` | Only these shells |
| `--session=<name>` | Name the session (so you can resume it) |
| `--restore[=<name>]` | Resume a session |
| `--pot=<file>` | Use a custom potfile (default `~/.john/john.pot`) |
| `--fork=<n>` | CPU parallelism (n processes) |
| `--list=formats` | List all supported formats |
| `--list=rules` | List all rule sets |
| `--test` | Benchmark |

---

## 🧪 Common format names

| `--format=` | Hash type | How it looks |
|---|---|---|
| `raw-md5` | MD5 | `5f4dcc3b5aa765d61d8327deb882cf99` |
| `raw-sha1` | SHA-1 | 40 hex chars |
| `raw-sha256` | SHA-256 | 64 hex chars |
| `raw-sha512` | SHA-512 | 128 hex chars |
| `md5crypt` | `$1$...` Linux old | `$1$salt$hash` |
| `sha256crypt` | `$5$...` | |
| `sha512crypt` | **`$6$...` modern Linux shadow** | |
| `bcrypt` | `$2a$`/`$2b$`/`$2y$` | |
| `NT` | Windows NTLM | 32 hex chars |
| `LM` | Windows LM (legacy) | |
| `netntlm` | NTLMv1 challenge/response | |
| `netntlmv2` | **NTLMv2 (Responder)** | `user::domain:chal:resp:...` |
| `mscash` / `mscash2` | Domain Cached Credentials | |
| `krb5tgs` | Kerberoast TGS | `$krb5tgs$23$...` |
| `krb5asrep` | AS-REP roast | `$krb5asrep$23$...` |
| `zip` / `rar` / `7z` | Archives (via `*2john`) | |
| `ssh` | SSH private key | |
| `pdf` | PDF | |

List them all: `john --list=formats | less`

---

## 📋 Command recipes (copy-paste)

```bash
# 1. The dead-simple default — let john figure it out
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt

# 2. Linux /etc/shadow → unshadow → crack
unshadow /etc/passwd /etc/shadow > unshadow.txt
john --wordlist=/usr/share/wordlists/rockyou.txt unshadow.txt
john --show unshadow.txt

# 3. NTLM hashes dumped from secretsdump.py
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt

# 4. NTLMv2 capture from Responder
john --format=netntlmv2 --wordlist=rockyou.txt responder.hash

# 5. Kerberoast
john --format=krb5tgs --wordlist=rockyou.txt tgs.hash

# 6. Crack a ZIP
zip2john protected.zip > zip.hash
john --wordlist=rockyou.txt zip.hash

# 7. SSH private key passphrase
ssh2john id_rsa > ssh.hash
john --wordlist=rockyou.txt ssh.hash

# 8. Wordlist + mangling rules (try variations of each word)
john --wordlist=rockyou.txt --rules=Jumbo hash.txt

# 9. Mask — 6 lowercase letters
john --mask='?l?l?l?l?l?l' hash.txt

# 10. Named session + resume
john --session=night --wordlist=rockyou.txt hash.txt
# Ctrl+C
john --restore=night

# 11. Hand uncracked hashes off to hashcat
john --show=left hash.txt > uncracked.txt
```

---

## ⚠️ Gotchas

- **"No password hashes loaded"** → wrong format. Run `john --list=formats | grep -i <keyword>` and force with `--format=`.
- **"No password hashes left to crack (see FAQ)"** → they're already in `~/.john/john.pot`. Run `john --show hash.txt`, or delete/rename the pot file to re-crack.
- **rockyou.txt missing on Parrot?** `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`.
- **Shadow file needs `unshadow`** — john won't crack `/etc/shadow` directly; it needs the merged `passwd:shadow` format.
- **`--format=NT` vs `nt` vs `NTLM`** — jumbo accepts `NT`; older john uses `nt`. Tab-complete or check `--list=formats`.
- **Single mode first, always** — `john --single` is free wins (uses username as password, etc.) before burning time on wordlists.
- **GPU cracking?** john uses CPU by default; if you have a GPU, [hashcat](./hashcat.md) will be 10-100x faster.
- **Rules file location:** `/etc/john/john.conf` (or `/usr/share/john/john.conf`) — add custom `[List.Rules:MyRule]` sections here.
- **Potfile bleed** — if you're debugging, set `--pot=./local.pot` so you're not seeing leftovers from another hash set.

---

## 🔗 Related

- [hashcat](./hashcat.md) — GPU cracking, same goal, faster
- [hashid](./hashid.md) — identify hash type first
- [hydra](./hydra.md) — online brute force instead of offline
- [crackmapexec](./crackmapexec.md) — dumps hashes you then crack with john
