# sslscan — SSL/TLS cipher + cert scanner

> **Fast TLS config auditor.** Lists supported protocols, ciphers, cert chain, and highlights weak options in red.

**Install check:** `sslscan --version`

---

## 🎯 Cheat-flow

```bash
sslscan target.com                               # default :443
sslscan target.com:8443                          # custom port
sslscan --no-colour target.com                   # plain output
sslscan --show-certificate target.com            # full cert
sslscan --ssl2 --ssl3 --tls10 target.com         # force specific versions
sslscan --starttls-smtp mail.target.com:25       # STARTTLS upgrade
sslscan --starttls-ftp ftp.target.com:21
sslscan --starttls-imap imap.target.com:143
sslscan --starttls-pop3 pop.target.com:110
sslscan --xml=scan.xml target.com
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `--no-colour` | Plain output |
| `--show-certificate` | Print cert details |
| `--show-client-cas` | List accepted client CAs |
| `--no-check-certificate` | Skip cert validation |
| `--ssl2`/`--ssl3`/`--tls10`/`--tls11`/`--tls12`/`--tls13` | Force protocol |
| `--starttls-<proto>` | STARTTLS for SMTP/FTP/IMAP/POP3/XMPP/MySQL/PSQL |
| `--ciphers=<list>` | Test specific cipher string |
| `--no-failed` | Hide rejected ciphers |
| `--xml=<file>` | XML output |

---

## 📋 What to look for in the output

| Red flag | Meaning |
|---|---|
| **SSLv2 / SSLv3 enabled** | Critical — POODLE, DROWN |
| **TLS 1.0 / 1.1 enabled** | Deprecated |
| **RC4** cipher | Broken |
| **NULL / EXPORT / anonymous** ciphers | No security |
| **3DES** | SWEET32 |
| **CBC with TLS 1.0** | BEAST |
| **Heartbeat enabled** | Heartbleed candidate (if OpenSSL vulnerable version) |
| Cert expired / self-signed / weak hash (MD5/SHA1) | Obvious |
| Cert key < 2048 bit RSA | Weak |

---

## 📋 Recipes

```bash
# 1. Full audit
sslscan --show-certificate target.com > scan.txt

# 2. STARTTLS SMTP
sslscan --starttls-smtp mail.target.com:25

# 3. Compare with nmap (cross-check)
nmap --script ssl-enum-ciphers -p 443 target.com

# 4. Test Heartbleed specifically
nmap --script ssl-heartbleed -p 443 target.com
```

---

## ⚠️ Gotchas

- SNI: modern hosts require correct hostname — use `sslscan target.com` not the IP.
- Versions of sslscan vary in which protocols they even compile with (SSLv2 is dropped in some builds). Use `--version` to check.
- For CVE-specific checks (Heartbleed, ROBOT, CCS Injection) use [nmap](nmap.md) NSE or [testssl.sh](https://testssl.sh).

---

## 🔗 Related

- [openssl](openssl.md) · [nmap](nmap.md) (`ssl-*` scripts) · [nikto](nikto.md)
