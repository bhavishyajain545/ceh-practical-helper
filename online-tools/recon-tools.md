# 🔍 Recon Tools (Online)

> WHOIS, DNS, subdomain enum, banner grab — sab Firefox se.

## WHOIS / Domain info

| Tool | URL | Use |
|---|---|---|
| **WHOIS.domaintools** | https://whois.domaintools.com/ | Detailed WHOIS with history |
| **who.is** | https://who.is/ | Quick WHOIS, multiple lookups |
| **ICANN Lookup** | https://lookup.icann.org/ | Official ICANN |
| **Hurricane Electric BGP** | https://bgp.he.net/ | ASN, IP ranges, BGP info |

---

## DNS recon

| Tool | URL | Use |
|---|---|---|
| **DNSDumpster** | https://dnsdumpster.com/ | **Best free.** Subdomain enum + visual DNS map + MX/NS records |
| **SecurityTrails** | https://securitytrails.com/ | DNS history (when records changed) |
| **MXToolbox** | https://mxtoolbox.com/ | All DNS lookups: MX, A, AAAA, CNAME, TXT, SPF, DMARC |
| **ViewDNS.info** | https://viewdns.info/ | 20+ DNS tools in one page |
| **DNSChecker** | https://dnschecker.org/ | Global DNS propagation check |

---

## Subdomain enumeration

| Tool | URL | Use |
|---|---|---|
| **Sublist3r web** | https://github.com/aboul3la/Sublist3r | (terminal — see [tools/sublist3r.md](../tools/sublist3r.md)) |
| **crt.sh** | https://crt.sh/ | **Certificate transparency** — find subdomains via SSL certs. Search `%.example.com` |
| **Subdomain Finder** | https://subdomainfinder.c99.nl/ | Multi-source aggregator |
| **DNSdumpster** | https://dnsdumpster.com/ | Also does subdomains |
| **Pentest-tools.com Subdomains** | https://pentest-tools.com/information-gathering/find-subdomains-of-domain | Free with rate limit |
| **VirusTotal Subdomains** | https://www.virustotal.com/gui/domain/<DOMAIN>/relations | Hidden gem — VT logs subdomains it has seen |

**Best workflow:**
1. crt.sh — `%.target.com` — find all SSL-cert subdomains
2. DNSDumpster — visual + records
3. VirusTotal — for any orphans

---

## Banner / IoT / Internet device search

| Tool | URL | Use |
|---|---|---|
| **Shodan** | https://www.shodan.io/ | **The one.** IP, banner, country, org search. Free account = limited but useful |
| **Censys** | https://search.censys.io/ | Shodan alternative, sometimes better SSL data |
| **ZoomEye** | https://www.zoomeye.org/ | Chinese alternative, more raw |
| **Onyphe** | https://www.onyphe.io/ | Aggregator |
| **GreyNoise** | https://www.greynoise.io/ | "Internet noise" — see if IP is benign scanner |
| **FOFA** | https://fofa.info/ | Chinese version of Shodan |

**Shodan filters cheat:**
- `port:22` — SSH only
- `country:IN` — India only
- `org:"Example Corp"` — by org
- `product:Apache` — by software
- `vuln:CVE-2017-0144` — vulnerable hosts (paid feature)
- `hostname:.example.com`
- `net:10.10.10.0/24` — by IP range

---

## IP geolocation / info

| Tool | URL | Use |
|---|---|---|
| **ipinfo.io** | https://ipinfo.io/ | Best free — geo, ISP, ASN |
| **iplocation.net** | https://www.iplocation.net/ | Multiple sources combined |
| **ipchicken.com** | http://www.ipchicken.com/ | Quick "what's my IP" |
| **ip-api.com** | http://ip-api.com/ | API-style, fast |

---

## Email / username enum

| Tool | URL | Use |
|---|---|---|
| **Hunter.io** | https://hunter.io/ | Find emails for a domain |
| **EmailHippo** | https://tools.emailhippo.com/ | Verify email exists |
| **HaveIBeenPwned** | https://haveibeenpwned.com/ | Check breach exposure |
| **DeHashed** | https://dehashed.com/ | Breach search (paid) |
| **NameCheckr** | https://www.namecheckr.com/ | Username availability across platforms |

---

## Google dorks (zero-cost recon)

| Dork | Kya milta hai |
|---|---|
| `site:target.com` | All indexed pages |
| `site:target.com -www` | Subdomains other than www |
| `site:target.com filetype:pdf` | PDFs leaked |
| `site:target.com filetype:xls OR filetype:xlsx` | Spreadsheets |
| `site:target.com inurl:admin` | Admin panels |
| `site:target.com intitle:"index of"` | Open directories |
| `site:target.com ext:sql OR ext:bak OR ext:old` | Backup files |
| `site:target.com inurl:login` | Login pages |
| `"target.com" password` | Mentions of "password" + target |
| `intitle:"index of /" "target.com"` | Open dirs hosting target files |

→ Full list in [google-dorks.md](../tools/google-dorks.md)

---

## Wayback Machine

| Tool | URL | Use |
|---|---|---|
| **Wayback Machine** | https://web.archive.org/ | Historical snapshots — find old admin panels, leaked endpoints |
| **Wayback URLs CLI** | https://github.com/tomnomnom/waybackurls | Bulk URL extraction |

**Use:** `https://web.archive.org/web/*/target.com/*` — see all archived URLs

---

## ⚠️ Gotchas

- **Rate limits:** crt.sh, DNSDumpster har 30 sec mein 1 query
- **Free Shodan account:** scans aur exports limited; bare search free hai
- **Privacy:** in tools mein "passive" recon hai — target ko nahi pata chalega (vs active nmap)
- **Combine sources:** ek tool kabhi complete nahi — 2-3 cross-check karo
