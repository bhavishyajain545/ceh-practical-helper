# theHarvester — OSINT emails / subdomains / hosts

> **Passive recon.** Scrapes search engines, certificate transparency, DNS, and public sources for emails, subdomains, IPs, and employee names of a target domain. Run early in footprinting.

**Install check (Parrot — already installed):** `theHarvester -h`
(also aliased as `theharvester` on some distros)

---

## 🎯 Cheat-flow: "What do I run?"

| You need to find... | Run this |
|---|---|
| **Emails + subdomains (default)** | `theHarvester -d example.com -b all` |
| Fast scrape, one source | `theHarvester -d example.com -b duckduckgo` |
| Cert transparency (best subdomains) | `theHarvester -d example.com -b crtsh` |
| Bing only | `theHarvester -d example.com -b bing` |
| Limit results | `theHarvester -d example.com -b all -l 500` |
| Save report (HTML + XML) | `theHarvester -d example.com -b all -f report` |
| Pipe through virtual host + DNS brute | `theHarvester -d example.com -b all -v -c` |
| Shodan lookup (needs API key) | `theHarvester -d example.com -b shodan` |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-d <domain>` | **Target domain** (required) |
| `-b <source>` | **Data source(s)** — comma-sep, or `all` |
| `-l <N>` | Limit results per source |
| `-s <N>` | Start index (pagination) |
| `-f <basename>` | Save to `basename.html` / `basename.xml` / `basename.json` |
| `-v` | **Verify** virtual hosts by resolving found names |
| `-c` | **DNS brute-force** common subdomains |
| `-n` | Reverse DNS on found IP range |
| `-r` | DNS TLD expansion |
| `-t` | Takeover check on found subdomains |
| `-p` | Port scan found hosts (top ports) |

### Common sources (`-b`)
`anubis`, `baidu`, `bing`, `brave`, `crtsh`, `duckduckgo`, `github-code`, `hackertarget`, `hunter`, `intelx`, `otx`, `rapiddns`, `securityTrails`, `shodan`, `threatminer`, `urlscan`, `virustotal`, `yahoo`, `all`.

> Sources requiring API keys (shodan, hunter, securityTrails, intelx, github-code, virustotal) need entries in `~/.theHarvester/api-keys.yaml`.

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Fast broad run (all free sources)
theHarvester -d example.com -b all -f harvester

# 2. Most useful single source — cert transparency
theHarvester -d example.com -b crtsh -l 1000

# 3. DuckDuckGo + Bing (no API keys needed)
theHarvester -d example.com -b duckduckgo,bing -l 500

# 4. With DNS brute + vhost verify
theHarvester -d example.com -b all -c -v -l 500

# 5. Save HTML/XML/JSON report
theHarvester -d example.com -b all -f example_report

# 6. Just emails (parse after)
theHarvester -d example.com -b all | grep @example.com

# 7. Subdomain takeover check
theHarvester -d example.com -b crtsh -t

# 8. From the XML — extract subdomain list
xmllint --xpath '//host/text()' example_report.xml 2>/dev/null | sort -u
```

---

## ⚠️ Gotchas

- **Rate-limiting / CAPTCHAs** on Google/Yahoo kill runs — prefer `bing`, `duckduckgo`, `crtsh`.
- **`-b all`** is noisy and slow; expect errors per source. Read through the summary.
- **API keys** live in `~/.theHarvester/api-keys.yaml`. Without them, Shodan/Hunter/etc. return nothing.
- **Output file basename** — don't include extension; theHarvester adds `.html/.xml/.json`.
- **Verify subdomains** (`-v`) with live DNS before believing them — scraping returns dead entries.
- **Results ≠ in-scope.** Always confirm scope before poking anything you find.
- **Exam tip:** for CEH questions, `-b baidu` or `-b bing` is usually enough and finishes fast.

---

## 🔗 Related

- [dig](./dig.md) — verify any subdomain you find
- [dnsrecon](./dnsrecon.md) — alternative DNS + brute
- [whois](./whois.md) — domain owner / registrar
- `sublist3r`, `amass`, `assetfinder` — alternative subdomain tools
- [footprinting playbook](../playbooks/footprinting-playbook.md)
