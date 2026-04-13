# recon-ng — modular OSINT framework

> **Metasploit-style console for passive recon.** Modules pull from Shodan, HIBP, Whois, Hunter, Github, etc. into a SQLite workspace.

**Launch:** `recon-ng`

---

## 🎯 Cheat-flow

```text
[recon-ng][default] > workspaces create target
[recon-ng][target]  > marketplace search
[recon-ng][target]  > marketplace install recon/domains-hosts/hackertarget
[recon-ng][target]  > modules load recon/domains-hosts/hackertarget
[recon-ng][target][hackertarget] > info
[recon-ng][target][hackertarget] > options set SOURCE target.com
[recon-ng][target][hackertarget] > run
[recon-ng][target]  > show hosts
[recon-ng][target]  > db query SELECT host FROM hosts
```

---

## 🔑 Core commands

| Command | Purpose |
|---|---|
| `workspaces create/load/remove <name>` | Per-engagement DB |
| `marketplace search <kw>` | Find modules |
| `marketplace install <path>` | Install |
| `marketplace install all` | Install everything |
| `modules load <path>` | Load module |
| `info` | Module description + options |
| `options set KEY value` | Set module option |
| `run` | Execute |
| `keys list` | Show API key slots |
| `keys add shodan_api <KEY>` | Store an API key |
| `show hosts / contacts / credentials / domains / vulnerabilities` | Dump tables |
| `db query <SQL>` | Raw SQLite |
| `dashboard` | Status across modules |
| `spool start file.txt` | Tee output |

---

## 🧩 Useful modules

| Path | What it gives you |
|---|---|
| `recon/domains-hosts/hackertarget` | Subdomains from hackertarget.com |
| `recon/domains-hosts/bing_domain_web` | Subdomains via Bing |
| `recon/domains-hosts/google_site_web` | Google-dork scraping |
| `recon/domains-hosts/certificate_transparency` | CT logs |
| `recon/domains-hosts/shodan_hostname` | Shodan |
| `recon/domains-contacts/whois_pocs` | Whois contacts |
| `recon/domains-contacts/hunter_io` | Email harvest |
| `recon/profiles-profiles/profiler` | Username across sites |
| `recon/hosts-hosts/resolve` | DNS resolve |
| `recon/hosts-hosts/reverse_resolve` | Reverse DNS |
| `recon/hosts-ports/shodan_ip` | Shodan per IP |
| `recon/netblocks-hosts/shodan_net` | Shodan per netblock |
| `reporting/html` | Generate HTML report |
| `reporting/csv` | CSV export |

---

## 📋 Recipe: full domain OSINT

```text
workspaces create corp
marketplace install recon/domains-hosts/hackertarget \
                    recon/domains-hosts/certificate_transparency \
                    recon/domains-contacts/whois_pocs \
                    reporting/html

modules load recon/domains-hosts/hackertarget
options set SOURCE target.com
run

modules load recon/domains-hosts/certificate_transparency
options set SOURCE target.com
run

show hosts
modules load reporting/html
options set CREATOR me
options set CUSTOMER target
options set FILENAME /tmp/report.html
run
```

---

## ⚠️ Gotchas

- v5+ split from core — modules now live in **marketplace**, not preloaded. `marketplace install all` gets everything.
- Many modules need **API keys** — `keys add <name> <value>`. Module `info` lists required keys.
- Workspace DB at `~/.recon-ng/workspaces/<name>/data.db`.
- Rate-limits silently kill free APIs — check `dashboard` for failures.

---

## 🔗 Related

- [theharvester](theharvester.md) · [sublist3r](sublist3r.md) · [shodan](shodan.md) · [whois](whois.md) · [dnsrecon](dnsrecon.md)
