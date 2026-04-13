# hash-identifier — interactive hash type guesser

> **Python sibling of [hashid](hashid.md).** Paste a hash, get a list of possible algorithms.

**Launch:** `hash-identifier`

---

## 🎯 Cheat-flow

```text
$ hash-identifier
HASH: 5f4dcc3b5aa765d61d8327deb882cf99

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

Least Possible Hashs:
[+] RAdmin v2.x
[+] NTLM
...
```

Quit with `q`.

---

## 🔑 vs hashid

| Tool | Best for |
|---|---|
| `hashid` | Scriptable (`-m` shows hashcat mode) |
| `hash-identifier` | Interactive single-hash questions |

```bash
hashid -m '5f4dcc3b5aa765d61d8327deb882cf99'
# → MD5 ... [Hashcat Mode: 0]
```

For CEH exam questions asking "what hashcat mode?" prefer `hashid -m`.

---

## 📋 Quick recipe

```bash
# 1. Identify
hashid -m '$1$abc$...'

# 2. Look up mode → crack
hashcat -m 500 hash.txt rockyou.txt    # md5crypt
```

---

## ⚠️ Gotchas

- Multiple matches for short/generic hashes (MD5 vs NTLM vs LM) — context (file source, prefix) resolves it.
- Modern prefix-tagged hashes (`$2y$`, `$6$`, `$1$`, `{SSHA}`) are unambiguous.
- Always prefer feeding the full line including any `salt:` prefix so identifier can pattern-match.

---

## 🔗 Related

- [hashid](hashid.md) · [hashcat](hashcat.md) · [john](john.md)
