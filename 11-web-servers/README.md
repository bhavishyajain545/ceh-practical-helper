# 11 — Web Servers

> Web server questions usually hand you an IP + port 80/443 and ask "what server", "what version", or "find the hidden directory". Fast fingerprint → dir enum → known CVE lookup.

## 🧭 Decision tree — "I see a web server question"

```
Port 80/443/8080/8443 open?
│
├── "What web server / version"
│   ├── curl -I http://<IP>           → read Server: header
│   └── whatweb http://<IP>           → tech stack + version
│
├── "Is there a WAF"
│   └── wafw00f http://<IP>
│
├── "Find hidden directories / files"
│   ├── gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt
│   ├── ffuf -u http://<IP>/FUZZ -w <wordlist>
│   └── dirb http://<IP>
│
├── "Find known vulns on this web server"
│   └── nikto -h http://<IP>
│
├── "What technology / CMS"
│   └── whatweb -a 3 http://<IP>
│
└── "Backup / config file" (.bak, .old, robots.txt)
    ├── curl http://<IP>/robots.txt
    └── ffuf -u http://<IP>/FUZZ -w raft-small-files.txt -e .bak,.old,.zip
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/by-domain/11-web-servers.md](../questions/by-domain/11-web-servers.md)**

## 🛠 Tools used in this domain

- **[nikto](../tools/nikto.md)** — known-vuln web scanner
- **[gobuster](../tools/gobuster.md)** — dir / file / vhost brute force
- **[ffuf](../tools/ffuf.md)** — fast fuzzer
- **[dirb](../tools/dirb.md)** — classic dir brute force
- **[whatweb](../tools/whatweb.md)** — fingerprinter
- **[wafw00f](../tools/wafw00f.md)** — WAF detector
- **[curl](../tools/curl.md)** — banner grab / header read

## ✅ Domain checklist

- [ ] Banner grab with `curl -I` from memory
- [ ] Run nikto against a target and read output for known CVEs
- [ ] Run gobuster OR ffuf with common.txt wordlist
- [ ] Know where wordlists live (`/usr/share/wordlists/dirb/`, `/usr/share/seclists/`)
- [ ] Detect a WAF with wafw00f
- [ ] Identify CMS with whatweb (WordPress → jump to [12-web-apps](../12-web-apps/README.md))
- [ ] Read `robots.txt` and `/sitemap.xml` manually
- [ ] Done all questions in [the question bank](../questions/by-domain/11-web-servers.md)
