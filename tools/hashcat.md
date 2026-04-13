# hashcat — GPU hash cracker

> **The fastest offline cracker, period.** Whole game is: pick the right `-m` (hash mode), pick the right `-a` (attack mode), point it at a wordlist or mask. For CPU-side auto-detection use [john](./john.md); to identify the hash first use [hashid](./hashid.md).

**Install check (Parrot — already installed):** `hashcat --version` · benchmark: `hashcat -b`

---

## 🎯 Cheat-flow: "What do I crack?"

| You need to crack... | Run this | Mode |
|---|---|---|
| **MD5** | `hashcat -m 0 -a 0 hash.txt rockyou.txt` | `0` |
| **SHA1** | `hashcat -m 100 -a 0 hash.txt rockyou.txt` | `100` |
| **SHA256** | `hashcat -m 1400 -a 0 hash.txt rockyou.txt` | `1400` |
| **SHA512** | `hashcat -m 1700 -a 0 hash.txt rockyou.txt` | `1700` |
| **NTLM (Windows)** | `hashcat -m 1000 -a 0 hash.txt rockyou.txt` | `1000` |
| **NetNTLMv2 (Responder)** | `hashcat -m 5600 -a 0 hash.txt rockyou.txt` | `5600` |
| **Kerberoast TGS** | `hashcat -m 13100 -a 0 hash.txt rockyou.txt` | `13100` |
| **AS-REP roast** | `hashcat -m 18200 -a 0 hash.txt rockyou.txt` | `18200` |
| **sha512crypt `$6$`** | `hashcat -m 1800 -a 0 hash.txt rockyou.txt` | `1800` |
| **md5crypt `$1$`** | `hashcat -m 500 -a 0 hash.txt rockyou.txt` | `500` |
| **bcrypt `$2*$`** | `hashcat -m 3200 -a 0 hash.txt rockyou.txt` | `3200` |
| **WPA/WPA2 (.hc22000)** | `hashcat -m 22000 -a 0 capture.hc22000 rockyou.txt` | `22000` |
| **Wordlist + rules** | `hashcat -m 0 -a 0 hash.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule` | `-a 0` |
| **Mask brute force (8 digits)** | `hashcat -m 0 -a 3 hash.txt ?d?d?d?d?d?d?d?d` | `-a 3` |
| **Combinator (word1 + word2)** | `hashcat -m 0 -a 1 hash.txt a.txt b.txt` | `-a 1` |
| **Hybrid wordlist + mask** | `hashcat -m 0 -a 6 hash.txt rockyou.txt ?d?d?d` | `-a 6` |
| Show cracked so far | `hashcat -m <mode> hash.txt --show` | |
| Running but stuck? | press `s` status, `p` pause, `r` resume, `q` quit | |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-m <n>` | **Hash mode** (see table below) |
| `-a <n>` | **Attack mode** — `0`=wordlist, `1`=combinator, `3`=mask, `6`=wordlist+mask, `7`=mask+wordlist |
| `-o <file>` | Write results to file |
| `--show` | Print cracked hashes from the potfile |
| `--left` | Print uncracked hashes only |
| `--username` | Input file has `user:hash` format (strip the user) |
| `-r <rules>` | Apply a rules file |
| `--force` | Ignore warnings (needed in VMs w/o real GPU) |
| `-O` | Optimized kernels (faster, pw length capped) |
| `-w <n>` | Workload 1–4 (3 default, 4 = hog the GPU) |
| `--status` | Auto-print status line |
| `--session=<name>` | Named session |
| `--restore` | Resume a named session |
| `--potfile-path=<f>` | Use custom potfile |
| `--remove` | Remove cracked hashes from input file |
| `-i` | Increment mask length from `--increment-min` to `--increment-max` |
| `--stdout` | Don't crack — just print candidate words (useful for debugging rules/masks) |

---

## 🧪 Attack modes (`-a`)

| `-a` | Name | Input |
|---|---|---|
| `0` | **Straight** (wordlist) | `hash.txt words.txt` |
| `1` | **Combinator** (word1 + word2) | `hash.txt a.txt b.txt` |
| `3` | **Brute-force / mask** | `hash.txt ?l?l?l?l?d?d` |
| `6` | **Hybrid** wordlist + mask | `hash.txt words.txt ?d?d?d` |
| `7` | **Hybrid** mask + wordlist | `hash.txt ?d?d?d words.txt` |
| `9` | Association (one cand per hash) | rarely needed |

### Mask character sets

| Token | Chars |
|---|---|
| `?l` | a-z |
| `?u` | A-Z |
| `?d` | 0-9 |
| `?h` | 0-9 a-f (lower hex) |
| `?H` | 0-9 A-F (upper hex) |
| `?s` | special `!@#$%^&*()...` |
| `?a` | all printable ASCII (`?l?u?d?s`) |
| `?b` | 0x00–0xff |
| Literal | any other char |

Custom charset: `-1 ?l?d` then `?1?1?1?1` uses that set.

---

## 🧪 Hash modes (`-m`) — the ones you need for CEH

| `-m` | Hash | Example / notes |
|---|---|---|
| `0` | MD5 | `5f4dcc3b5aa765d61d8327deb882cf99` |
| `100` | SHA1 | 40 hex |
| `1400` | SHA256 | 64 hex |
| `1700` | SHA512 | 128 hex |
| `10` | md5($pass.$salt) | salted md5 |
| `20` | md5($salt.$pass) | salted md5 |
| `500` | md5crypt `$1$` | old Linux |
| `1800` | **sha512crypt `$6$` — modern /etc/shadow** | |
| `7400` | sha256crypt `$5$` | |
| `3200` | bcrypt `$2a$`/`$2b$`/`$2y$` | slow |
| `900` | MD4 | |
| `1000` | **NTLM** (Windows local / AD NT hash) | |
| `3000` | LM (legacy, 7-char halves) | |
| `5500` | NetNTLMv1 | |
| `5600` | **NetNTLMv2** (Responder) | |
| `13100` | **Kerberoast (TGS-REP etcype 23)** | `$krb5tgs$23$...` |
| `19700` | Kerberoast (etype 17) | |
| `18200` | **AS-REP roast** (etype 23) | `$krb5asrep$23$...` |
| `2100` | Domain Cached Credentials 2 (mscash2) | |
| `22000` | **WPA-PBKDF2-PMKID+EAPOL** (new unified) | from `hcxpcapngtool` |
| `2500` | WPA/WPA2 EAPOL (legacy `.hccapx`) | deprecated, use 22000 |
| `16800` | WPA PMKID | legacy |
| `11600` | 7-Zip | via `7z2hashcat` |
| `13400` | KeePass | |
| `9600` | Office 2013 | |
| `9400` | Office 2007 | |

Full list: `hashcat --help | less` or `hashcat --example-hashes | less` (shows an example for every mode).

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Bread-and-butter: NTLM + rockyou + best64 rules
hashcat -m 1000 -a 0 ntlm.txt /usr/share/wordlists/rockyou.txt \
  -r /usr/share/hashcat/rules/best64.rule -O

# 2. Show what you've cracked
hashcat -m 1000 ntlm.txt --show

# 3. Linux shadow ($6$) — SLOW, use a small wordlist
hashcat -m 1800 -a 0 shadow.hash /usr/share/wordlists/rockyou.txt

# 4. Kerberoast TGS
hashcat -m 13100 -a 0 tgs.hash /usr/share/wordlists/rockyou.txt -O

# 5. AS-REP roast
hashcat -m 18200 -a 0 asrep.hash /usr/share/wordlists/rockyou.txt -O

# 6. NetNTLMv2 from Responder
hashcat -m 5600 -a 0 responder.hash /usr/share/wordlists/rockyou.txt

# 7. Mask — 8 digits (PIN, phone, date)
hashcat -m 0 -a 3 md5.hash ?d?d?d?d?d?d?d?d

# 8. Mask — capital, 5 lowers, 2 digits (e.g. Passw0rd-style)
hashcat -m 1000 -a 3 ntlm.txt ?u?l?l?l?l?l?d?d

# 9. Hybrid — rockyou word + 4 digits appended
hashcat -m 0 -a 6 md5.hash /usr/share/wordlists/rockyou.txt ?d?d?d?d

# 10. WPA2 from a .pcap
hcxpcapngtool -o cap.hc22000 capture.pcap
hashcat -m 22000 -a 0 cap.hc22000 /usr/share/wordlists/rockyou.txt

# 11. Benchmark / confirm GPU is detected
hashcat -b
hashcat -I      # list devices

# 12. Debug what your rules produce without cracking
hashcat -a 0 --stdout rockyou.txt -r best64.rule | head

# 13. Named session + resume
hashcat -m 1000 -a 0 hash.txt rockyou.txt --session=work
# Ctrl+C
hashcat --restore --session=work
```

**Rules on Parrot live at:** `/usr/share/hashcat/rules/` — the famous ones:
- `best64.rule` — fast, high-hit
- `rockyou-30000.rule` — heavy
- `d3ad0ne.rule` — classic
- `dive.rule` — aggressive

---

## ⚠️ Gotchas

- **Wrong `-m`** is the #1 mistake — a SHA1 run against an MD5 mode will do nothing and *not* error obviously. Always identify with [hashid](./hashid.md) first, and cross-check with `hashcat --example-hashes | grep -A2 MODE_<n>`.
- **Mode 22000 vs 2500** — `.hccapx` is deprecated. Always convert pcaps with `hcxpcapngtool -o out.hc22000 capture.pcap` and use `-m 22000`.
- **Running in a VM with no GPU** → add `--force` and expect slow speeds; certain heavy modes (bcrypt, scrypt, sha512crypt) are effectively unusable on CPU — keep wordlists small.
- **"Token length exception"** → the hash line has a username or extra whitespace. Strip it, or use `--username` if file is `user:hash`.
- **"Salt-value exception"** → wrong format for a salted mode (e.g. mode `10` expects `hash:salt`).
- **Potfile re-runs show nothing** → hashcat already cracked it. Run `--show`, or delete `~/.hashcat/hashcat.potfile`.
- **Mask vs wordlist confusion** — `-a 0` takes a **file**; `-a 3` takes a **mask string** (no file).
- **`-O` (optimized)** caps password length at 32 (or less for some modes). Drop `-O` if you need longer candidates.
- **rockyou path on Parrot:** `/usr/share/wordlists/rockyou.txt` (gunzip first if it's `.gz`).
- **Input file must be LF, not CRLF** — Windows-edited hash files fail silently. `dos2unix hash.txt` fixes it.

---

## 🔗 Related

- [john](./john.md) — auto-detecting CPU cracker, handy for weird formats & `*2john` converters
- [hashid](./hashid.md) — identify a hash before picking `-m`
- [hydra](./hydra.md) — online brute force instead of offline cracking
- [crackmapexec](./crackmapexec.md) — dumps NTLM hashes you then feed to `-m 1000`
