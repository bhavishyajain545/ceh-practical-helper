# joomscan — Joomla vulnerability scanner

> **OWASP project.** Fingerprints Joomla version, finds exposed components, and checks known CVEs.

**Install check:** `joomscan --version`

---

## 🎯 Cheat-flow

```bash
joomscan -u http://target/                 # standard scan
joomscan --url http://target/
joomscan -u http://target/ --ec            # enumerate components
joomscan -u http://target/ --cookie "name=val"
joomscan -u http://target/ --proxy http://127.0.0.1:8080
joomscan -u http://target/ --user-agent "Mozilla/5.0"
joomscan -u http://target/ --timeout 60
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-u <url>` | Target |
| `--ec` | Enumerate components |
| `--cookie <str>` | Send cookies |
| `--user-agent <str>` | Custom UA |
| `--random-agent` | Random UA |
| `--proxy <url>` | HTTP proxy |
| `--timeout <sec>` | Request timeout |

---

## 📋 What it checks

- Joomla version + fingerprinting
- Firewall / WAF detection
- Admin login page finder
- Backup and log files
- Common misconfigurations (robots.txt, readme)
- Component enumeration (`com_*`)
- Known CVE checks per component

Output is saved to `reports/<host>/` as text + HTML.

---

## ⚠️ Gotchas

- For **WordPress** use [wpscan](wpscan.md). For **Drupal** use `droopescan`.
- Old project — vuln DB is not as fresh as nuclei templates. Run both.
- `--ec` is essential — without it you only get the version and basics.

---

## 🔗 Related

- [wpscan](wpscan.md) · [nuclei](nuclei.md) · [whatweb](whatweb.md) · [nikto](nikto.md)
