# dnsrecon — DNS enumeration & brute-force

> **Python-based DNS recon.** Standard enum, zone transfer attempt, reverse lookups, subdomain brute-force, cache snooping — all in one tool.

**Install check (Parrot — already installed):** `dnsrecon -h`

---

## 🎯 Cheat-flow

| You need to... | Run this |
|---|---|
| **Standard enum** (SOA/NS/MX/A/TXT + AXFR attempt) | `dnsrecon -d example.com` |
| Specify std type | `dnsrecon -d example.com -t std` |
| **Zone transfer only** | `dnsrecon -d example.com -t axfr` |
| **Subdomain brute-force** | `dnsrecon -d example.com -t brt -D /usr/share/wordlists/dnsmap.txt` |
| **Reverse lookup a range** | `dnsrecon -r 10.10.10.0/24` |
| Cache snoop a resolver | `dnsrecon -t snoop -n <DNS> -D names.txt` |
| Google scraping | `dnsrecon -d example.com -t goo` |
| Query a specific DNS server | `dnsrecon -d example.com -n <DNS-IP>` |
| Save CSV/XML/JSON | `dnsrecon -d example.com --csv out.csv --xml out.xml --json out.json` |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-d <domain>` | **Target domain** |
| `-n <ns>` | Use this nameserver |
| `-r <range>` | Reverse lookup range (CIDR or start-end) |
| `-t <type>` | **Type of enum** (see below) |
| `-D <wordlist>` | Wordlist for brute / snoop |
| `-a` | Perform AXFR with NS records |
| `-s` | SPF record enum |
| `-b` | Bing subdomain search |
| `-y` | Yandex search |
| `-k` | DNSSEC crawl |
| `-w` | Deep whois analysis |
| `-z` | DNSSEC zone walk |
| `--threads N` | Threads |
| `--lifetime N` | Query timeout |
| `--csv` / `--xml` / `--json` | Output |

### `-t` types
| Type | Meaning |
|---|---|
| `std` | **Standard** — SOA, NS, A, AAAA, MX, SRV, plus AXFR |
| `rvl` | Reverse lookup of given CIDR |
| `brt` | **Brute-force** subdomains from wordlist |
| `srv` | SRV record enumeration |
| `axfr` | **AXFR against all NS** for the domain |
| `goo` | Google scraping |
| `bing` | Bing scraping |
| `snoop` | Cache snoop (needs `-n` + `-D`) |
| `tld` | TLD expansion |
| `zonewalk` | DNSSEC zone walk |

---

## 📋 Command recipes

```bash
# 1. First-move standard enum
dnsrecon -d example.com

# 2. Try zone transfer against every NS
dnsrecon -d example.com -t axfr

# 3. Subdomain brute-force (fast)
dnsrecon -d example.com -t brt \
  -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  --threads 20

# 4. Reverse sweep internal /24
dnsrecon -r 10.10.10.0/24 -n <internal-DNS>

# 5. Against target's internal DNS server
dnsrecon -d corp.local -n 10.10.10.5 -t std

# 6. Full enum with all outputs
dnsrecon -d example.com -t std,brt,srv,axfr \
  -D /usr/share/wordlists/dnsmap.txt \
  --csv dns.csv --json dns.json

# 7. DNSSEC zone walk (NSEC leaks)
dnsrecon -d example.com -t zonewalk
```

---

## ⚠️ Gotchas

- **DNS server matters.** Lab/exam networks often require `-n <target-DNS>` to see internal zones — otherwise you're querying your own resolver for nothing.
- **Wordlist path** on Parrot: `/usr/share/wordlists/dnsmap.txt`, `/usr/share/seclists/Discovery/DNS/*`. Pick the right size.
- **AXFR** success is jackpot — always try it before brute-forcing.
- **`-t std`** already includes an AXFR attempt, so a bare `dnsrecon -d` is a good first shot.
- **Threads too high** → rate-limited or false negatives; start at 10–20.
- **`--lifetime`** — increase to 5+ for slow resolvers.
- **Comma in `-t`** lets you chain types (`-t std,brt`).

---

## 🔗 Related

- [dig](./dig.md) — manual DNS lookups
- [theharvester](./theharvester.md) — OSINT subdomains
- [whois](./whois.md) — registration info
- `fierce`, `dnsenum`, `amass`, `sublist3r` — alternative tools
- [footprinting playbook](../playbooks/footprinting-playbook.md)
