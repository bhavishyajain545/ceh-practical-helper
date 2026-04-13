# 02 — Scanning

> Scanning questions are the **most common** category in CEH Practical. Expect 3–5 of your 20 questions to be pure scanning, plus another 5+ that *start* with scanning.

## 🧭 Decision tree — "I see a scanning question"

```
Question gives you an IP / range?
│
├── "Find live hosts" / "How many hosts up"
│   └── nmap -sn <range>           → count "Host is up" lines
│
├── "Find open ports on host X"
│   └── nmap -p- -T4 <IP>           → count / list open
│
├── "Find the version of service X" / "What version of FTP"
│   └── nmap -sV -p <port> <IP>     → read VERSION column
│
├── "What OS is running"
│   └── sudo nmap -O <IP>           → read OS guess
│
├── "Find vulnerable / outdated service"
│   └── nmap --script vuln -p- <IP>
│
├── "Find SMB shares / SMB version / SMB vuln"
│   └── nmap -p 445 --script "smb-*" <IP>
│
├── "Find HTTP server software / page title"
│   └── nmap -p 80,443 --script "http-title,http-headers" <IP>
│
├── "UDP service" (SNMP, DNS, TFTP)
│   └── sudo nmap -sU -p <port> <IP>
│
└── "Stealth / firewall present"
    └── nmap -sS -Pn -f <IP>        (fragmented SYN)
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/by-domain/02-scanning.md](../questions/by-domain/02-scanning.md)**

## 🛠 Tools used in this domain

- **[nmap](../tools/nmap.md)** ← the big one
- hping3 *(coming)*
- masscan *(coming)*
- netdiscover *(coming)*

## ✅ Domain checklist (master these in 3 weeks)

- [ ] Run all 8 nmap recipes from [commands.md](commands.md) from memory… or, more realistically, know exactly where to find them
- [ ] Know the difference between `-sS`, `-sT`, `-sU`, `-sA`, `-sN/sF/sX`
- [ ] Recognize what a `filtered` vs `closed` vs `open` port means
- [ ] Know the top 10 NSE scripts in [tools/nmap.md NSE table](../tools/nmap.md#nse-scripts-the-money-section)
- [ ] Done all 12 questions in [the question bank](../questions/by-domain/02-scanning.md)
