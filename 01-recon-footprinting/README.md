# 01 — Recon & Footprinting

> Passive information gathering. Expect 2–4 questions that start with a **domain name** (not an IP) and ask you to dig up registrant info, subdomains, emails, or DNS records. No packets touch the target's infrastructure beyond DNS queries.

## 🧭 Decision tree — "I see a recon question"

```
Question gives you a DOMAIN (e.g. example.com)?
│
├── "Who owns / registered / registrar / expiry"
│   └── whois <DOMAIN>                → read Registrant / Registrar fields
│
├── "Find emails / employees / usernames"
│   └── theHarvester -d <DOMAIN> -b all
│
├── "Find subdomains"
│   ├── sublist3r -d <DOMAIN>
│   ├── dnsrecon -d <DOMAIN> -t brt
│   └── google dork: site:<DOMAIN> -www
│
├── "DNS records (A / MX / NS / TXT / SOA)"
│   ├── dig <DOMAIN> <TYPE>
│   ├── host -t <TYPE> <DOMAIN>
│   └── nslookup -type=<TYPE> <DOMAIN>
│
├── "Zone transfer" / "AXFR"
│   ├── dig axfr @<NS> <DOMAIN>
│   └── dnsrecon -d <DOMAIN> -t axfr
│
├── "Find hosts/IPs in the public internet" (exposed)
│   └── shodan search <query>          → or https://shodan.io
│
├── "Reverse-DNS a CIDR / find hostnames"
│   └── dnsrecon -r <CIDR>
│
└── "Open-source intel / social media / archive"
    └── recon-ng + google dorks (see commands.md)
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/01-recon.md](../questions/01-recon.md)** *(coming)*

## 🛠 Tools used in this domain

- **[whois](../tools/whois.md)** — registrar data
- **[theHarvester](../tools/theHarvester.md)** — emails / subdomains / hosts
- **[dnsrecon](../tools/dnsrecon.md)** — DNS enumeration + zone transfer
- **[sublist3r](../tools/sublist3r.md)** — subdomain bruteforce
- **[dig](../tools/dig.md)** — DNS swiss army knife
- **[nslookup](../tools/nslookup.md)** — DNS fallback
- **[host](../tools/host.md)** — quick DNS lookup
- **[recon-ng](../tools/recon-ng.md)** — modular OSINT framework
- **[shodan](../tools/shodan.md)** — internet-wide device search
- **[google-dorks](../tools/google-dorks.md)** — search operator cheatsheet

## ✅ Domain checklist

- [ ] Run `whois` and find Registrar, Creation Date, Name Servers
- [ ] Enumerate subdomains three ways (sublist3r, dnsrecon brute, Google dork)
- [ ] Pull all MX, NS, TXT records with a single `dig` command
- [ ] Perform a zone transfer attempt and know what "Transfer failed" means
- [ ] Extract 10+ emails with theHarvester from a real domain
- [ ] Know 5 google dorks cold (`site:`, `filetype:`, `inurl:`, `intitle:`, `-www`)
- [ ] Shodan query by org, country, port, product
- [ ] Recognize when a question wants the **registrar** vs the **registrant**
