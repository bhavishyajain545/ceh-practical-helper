# #️⃣ HASH MILA — KAUNSA TYPE HAI?

---

## Quick Identification by Length / Format

| Length | Format | Type | Hashcat | John |
|---|---|---|---|---|
| 32 | hex | MD5 | 0 | raw-md5 |
| 32 | hex (Windows context) | NTLM | 1000 | nt |
| 40 | hex | SHA-1 | 100 | raw-sha1 |
| 40 | `*` + hex | MySQL 4.1+ | 300 | mysql-sha1 |
| 64 | hex | SHA-256 | 1400 | raw-sha256 |
| 128 | hex | SHA-512 | 1700 | raw-sha512 |

## By Prefix

| Prefix | Type | Hashcat | John |
|---|---|---|---|
| `$1$` | MD5-crypt | 500 | md5crypt |
| `$5$` | SHA-256-crypt | 7400 | sha256crypt |
| `$6$` | SHA-512-crypt | 1800 | sha512crypt |
| `$2a$` / `$2b$` | bcrypt | 3200 | bcrypt |
| `$apr1$` | Apache MD5 | 1600 | - |
| `$P$` / `$H$` | phpass (WordPress) | 400 | phpass |
| `$krb5asrep$` | AS-REP Roast | 18200 | krb5asrep |
| `$krb5tgs$` | Kerberoast | 13100 | krb5tgs |

## By Context

| Where Found | Likely Type |
|---|---|
| Linux /etc/shadow (`$6$...`) | SHA-512-crypt |
| Windows SAM dump | NTLM (32 hex) |
| MySQL users table | MySQL SHA1 (`*` + 40 hex) |
| Web app database | MD5 or bcrypt |
| WordPress database | phpass (`$P$...`) |
| WiFi handshake | WPA/WPA2 |

## Auto-Identify:
```bash
hashid '<PASTE_HASH_HERE>'
hashid '<HASH>' -m                         # show hashcat mode
hash-identifier                            # interactive
```

---

## CRACK CHEAT:
```bash
# John
john --format=FORMAT --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt

# Hashcat
hashcat -m MODE hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m MODE hash.txt --show
```
