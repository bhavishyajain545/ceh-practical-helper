# hashid — identify hash types

> **Step zero of any cracking task.** Before you run [john](./john.md) or [hashcat](./hashcat.md), you need to know *what* the hash is. `hashid` prints the most likely candidates and — crucially — gives you the matching hashcat `-m` mode and john `--format=` name with `-m`/`-j` flags.

**Install check (Parrot — already installed):** `hashid --version` · there's also an older interactive variant called `hash-identifier` — see [below](#hash-identifier-the-interactive-one).

---

## 🎯 Cheat-flow: "What hash is this?"

| You have... | Run this | Gives you |
|---|---|---|
| A single hash on the CLI | `hashid '<HASH>'` | Candidate types |
| A hash and you want **hashcat modes** | `hashid -m '<HASH>'` | `[Hashcat Mode: 1000]` etc |
| A hash and you want **john formats** | `hashid -j '<HASH>'` | `[JtR Format: nt]` etc |
| A file full of hashes | `hashid -m hashes.txt` | Per-line guesses |
| Show **every** match, not just popular ones | `hashid -e '<HASH>'` | Extended list |
| Interactive / legacy tool | `hash-identifier` | Old-school menu |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-e` | **Extended** — show every candidate, not just the common ones |
| `-m` | Include **hashcat mode** number in output |
| `-j` | Include **John the Ripper format** name in output |
| `-o <file>` | Write results to file |
| `-h` | Help |
| (positional) | A hash string **or** a path to a file of hashes |

That's it — hashid is deliberately tiny.

---

## 🧪 What hashid recognises (exam-relevant)

hashid checks signature-based patterns — length, charset, `$`-prefix, etc. It can identify 200+ types. The ones you'll actually meet in CEH:

| Hash | Looks like | hashcat `-m` | john `--format=` |
|---|---|---|---|
| MD5 | 32 hex | `0` | `raw-md5` |
| SHA-1 | 40 hex | `100` | `raw-sha1` |
| SHA-256 | 64 hex | `1400` | `raw-sha256` |
| SHA-512 | 128 hex | `1700` | `raw-sha512` |
| NTLM | 32 hex (indistinguishable from MD5!) | `1000` | `NT` |
| LM | 32 hex (two 16-hex halves) | `3000` | `LM` |
| md5crypt | `$1$salt$hash` | `500` | `md5crypt` |
| sha256crypt | `$5$salt$hash` | `7400` | `sha256crypt` |
| sha512crypt | `$6$salt$hash` | `1800` | `sha512crypt` |
| bcrypt | `$2a$` / `$2b$` / `$2y$` | `3200` | `bcrypt` |
| MySQL4.1+ | `*` + 40 hex | `300` | `mysql-sha1` |
| NetNTLMv1 | `user::HOST:...:...` | `5500` | `netntlm` |
| NetNTLMv2 | `user::DOMAIN:chal:hmac:blob` | `5600` | `netntlmv2` |
| Kerberos TGS | `$krb5tgs$23$*...` | `13100` | `krb5tgs` |
| Kerberos AS-REP | `$krb5asrep$23$...` | `18200` | `krb5asrep` |
| DCC2 / mscash2 | `$DCC2$...` | `2100` | `mscash2` |
| WPA/WPA2 | `WPA*01*...` / `.hc22000` | `22000` | n/a |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Identify a single hash — show hashcat mode + john format
hashid -m -j '5f4dcc3b5aa765d61d8327deb882cf99'
#  → [+] MD5
#    [Hashcat Mode: 0]
#    [JtR Format: raw-md5]

# 2. A file of hashes, one per line
hashid -m -j hashes.txt

# 3. Extended mode — see every candidate when it's ambiguous
hashid -e '$1$Salt1234$X2n7Jy8D9V3q0pqK.XN2M.'

# 4. Pipe into hashcat with the suggested mode
HASH='aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c'
hashid -m "$HASH"                 # tells you it's NTLM → mode 1000
echo "$HASH" > ntlm.txt
hashcat -m 1000 -a 0 ntlm.txt /usr/share/wordlists/rockyou.txt

# 5. Bulk identify then hand off
hashid -m -o identified.txt hashes.txt
```

---

## 🧩 `hash-identifier` (the interactive one)

The older Python tool `hash-identifier` is also on Parrot. It's a menu-driven REPL — you launch it, paste a hash, it prints guesses, paste another, etc.

```bash
hash-identifier
# HASH: 5f4dcc3b5aa765d61d8327deb882cf99
# Possible Hashs: MD5 / ...
# HASH: <next hash>
```

Differences vs `hashid`:

| | `hashid` | `hash-identifier` |
|---|---|---|
| Usage | CLI, scriptable | Interactive REPL only |
| Output | Per-hash, with `-m`/`-j` mode mappings | Just a list of names |
| File input | Yes (`hashid file.txt`) | No |
| Accuracy | More modern signatures | Older, Kali-classic |
| Best for | Scripting, quick `-m` lookup | Paste-and-go during exam |

**Rule of thumb:** use `hashid -m -j` for everything. Keep `hash-identifier` as a fallback when hashid's guess looks wrong.

---

## ⚠️ Gotchas

- **MD5 vs NTLM are both 32 hex characters** and hashid will list **both**. Context decides: if it came from `/etc/shadow` or a web DB → MD5; from Windows SAM / `secretsdump` / LSASS → NTLM.
- **Salted vs unsalted** — a raw `5f4dcc3b...` is unsalted; `hash:salt` needs a salted mode in hashcat (e.g. `-m 10` md5($pass.$salt)). hashid won't tell you *which* salted variant — you may need to guess.
- **Quote the hash** on the shell. `$1$`, `$6$`, `!`, backticks — all get eaten by bash. Use single quotes.
- **Multiple guesses is normal** — hashid returns everything that matches the pattern. Try the most common first; if it fails, walk down the list.
- **Not on PATH?** On Parrot it's `/usr/bin/hashid`. Older Kali sometimes ships only `hash-identifier`.
- **Hashcat-specific formats** (`$krb5tgs$`, `$krb5asrep$`, `$DCC2$`) — hashid recognises the prefix. If it doesn't match anything, search the prefix on hashcat's example-hashes page: `hashcat --example-hashes | less`.

---

## 🔗 Related

- [hashcat](./hashcat.md) — use the suggested `-m` mode
- [john](./john.md) — use the suggested `--format=` name
- [crackmapexec](./crackmapexec.md) — common source of NTLM hashes
- [hydra](./hydra.md) — if you have a live service instead of a hash
