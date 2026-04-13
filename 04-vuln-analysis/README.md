# 04 — Vulnerability Analysis

> Given a service, host, or web app — find a CVE, a missing patch, or a known weakness. Expect 2–3 questions. Usually the answer is a CVE ID, a CVSS score, or an exploit name.

## 🧭 Decision tree — "I need to find a vulnerability"

```
What am I analyzing?
│
├── Web app / HTTP server
│   ├── nikto -h http://<IP>             ← headers, outdated software, config
│   └── nmap -p 80,443 --script "http-vuln-*" <IP>
│
├── Network service with a banner/version
│   ├── searchsploit <PRODUCT> <VERSION> ← offline exploit-db search
│   ├── searchsploit -w <PRODUCT>        ← get CVE links
│   └── Google: "<VERSION> exploit" / "<VERSION> CVE"
│
├── Whole host, scripted
│   └── nmap --script vuln -p- <IP>      ← broad NSE vuln sweep
│
├── Whole host, full scanner
│   └── OpenVAS / Greenbone              ← authenticated + unauth
│
├── Known-vulnerable CMS (WordPress, Joomla, Drupal)
│   └── see [../06-web](../12-web-apps/README.md)     *(coming)*
│
└── Just need the CVSS / CVE detail
    └── https://nvd.nist.gov/vuln/search  (paste CVE)
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/by-domain/04-vuln-analysis.md](../questions/by-domain/04-vuln-analysis.md)** *(coming)*

## 🛠 Tools used in this domain

- **[nikto](../tools/nikto.md)** — classic web vuln scanner
- **[nmap-vuln-scripts](../tools/nmap.md#nse-scripts-the-money-section)** — NSE `vuln` category
- **[searchsploit](../tools/searchsploit.md)** — offline exploit-db CLI
- **[openvas](../tools/openvas.md)** — full vuln scanner (Greenbone)
- **[metasploit](../tools/metasploit.md)** *(cross-linked from 05)*

## ✅ Domain checklist

- [ ] Run `nikto -h http://<IP>` and interpret the `OSVDB-` findings
- [ ] Run `nmap --script vuln` and recognise the `VULNERABLE:` blocks
- [ ] `searchsploit` any service banner and read the top 3 results
- [ ] Convert a `searchsploit` PoC path to a real file with `searchsploit -m`
- [ ] Know where `/usr/share/exploitdb/` lives and how to grep it directly
- [ ] Launch an OpenVAS quick scan from the web UI
- [ ] Map a banner → CVE in under 60 seconds
- [ ] Know the big "signature" vulns by name: MS17-010, MS08-067, Shellshock, Heartbleed, Log4Shell
