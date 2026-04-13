# shodan — the search engine for devices

> **Internet-wide recon from one CLI.** Great for footprinting questions that ask about exposed services, banners, or specific orgs.

**Install:** `pip install shodan` → `shodan init <APIKEY>`

---

## 🎯 Cheat-flow

| Task | Command |
|---|---|
| Who am I (key + credits) | `shodan info` |
| Look up one IP | `shodan host 1.2.3.4` |
| Full host JSON | `shodan host --format json 1.2.3.4` |
| Search banner text | `shodan search apache` |
| Count results (free) | `shodan count 'port:22 country:US'` |
| Download results | `shodan download out 'apache country:DE'` |
| Parse a download | `shodan parse --fields ip_str,port out.json.gz` |
| My external IP | `shodan myip` |
| On-demand scan | `shodan scan submit 1.2.3.4` |
| Alerts (monitoring) | `shodan alert create "name" 1.2.3.0/24` |
| Domain info | `shodan domain target.com` |

---

## 🔍 Search filter reference

| Filter | Example |
|---|---|
| `port:` | `port:3389` |
| `country:` | `country:BR` |
| `city:` | `city:"Sao Paulo"` |
| `org:` | `org:"Amazon"` |
| `hostname:` | `hostname:target.com` |
| `net:` | `net:1.2.3.0/24` |
| `os:` | `os:"Windows 7"` |
| `product:` | `product:nginx` |
| `version:` | `version:1.18.0` |
| `http.title:` | `http.title:"admin login"` |
| `http.status:` | `http.status:200` |
| `http.html:` | `http.html:"wp-content"` |
| `ssl.cert.subject.cn:` | `ssl.cert.subject.cn:target.com` |
| `vuln:` | `vuln:CVE-2021-44228` |
| `has_screenshot:true` | VNC/RDP screenshots |
| `tag:` | `tag:malware` |

Combine with spaces (AND) and `-` (NOT): `apache -country:CN port:80`.

---

## 📋 Recipes

```bash
# 1. Find Windows 7 RDP worldwide
shodan search 'port:3389 os:"Windows 7"'

# 2. Exposed MongoDB with no auth
shodan search 'product:MongoDB "MongoDB Server Information"'

# 3. All hosts from a target org
shodan search 'org:"Example Corp"' --fields ip_str,port,hostnames

# 4. One-shot IP lookup (copy for reports)
shodan host 1.2.3.4

# 5. Download + parse (bigger result sets)
shodan download hosts 'apache country:JP'
shodan parse --fields ip_str,port,org hosts.json.gz

# 6. Vuln hunt by CVE
shodan search 'vuln:CVE-2017-0144'     # EternalBlue
```

---

## ⚠️ Gotchas

- **API credits** — `search` and `download` cost credits; `count` is free. Use `count` to size-check first.
- Free accounts can't see all filters (vuln:, tag:) — needs paid membership.
- Data is **historical** — a "port 22" result may already be closed.
- Web UI → [shodan.io](https://www.shodan.io) — same filters.
- Rate limit: ~1 req/sec.
- For exam Qs that ask "what does shodan say about X?" the **web UI** screenshot is usually easier.

---

## 🔗 Related

- [theharvester](theharvester.md) · [recon-ng](recon-ng.md) · [whois](whois.md) · [nmap](nmap.md)
