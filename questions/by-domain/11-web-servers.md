# Question Bank — Hacking Web Servers (12 questions)

> Realistic CEH-Practical-style web server fingerprinting + low-hanging-fruit questions. Steps link into individual tool docs — click any command to see the full reference.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Identify the web server software and version on 10.10.10.5

**Category:** Banner grab | **Tools:** `curl`, [whatweb](../../tools/whatweb.md)

**Steps:**
1. `curl -sI http://10.10.10.5 | grep -i server` ← Server header
2. `whatweb http://10.10.10.5` ← [why](../../tools/whatweb.md#basic-usage)
3. Fallback: `nmap -sV -p 80 10.10.10.5`

**Answer format:** `<software>/<version>` verbatim (e.g. `Apache/2.4.29`)

**Gotcha:** `ServerTokens Prod` strips version — try error pages (`/nonexistent`) or `OPTIONS /`.

---

### Q2 🟢 — Read robots.txt on 10.10.10.5

**Category:** Info disclosure | **Tools:** `curl`

**Steps:**
1. `curl -s http://10.10.10.5/robots.txt`
2. Or in browser: `http://10.10.10.5/robots.txt`
3. Note every `Disallow:` entry — those are often the flag paths.

**Answer format:** a path (e.g. `/secret/admin`) or a count of entries

**Gotcha:** CEH exam sometimes wants the exact `Disallow:` line verbatim, including the leading slash.

---

### Q3 🟢 — Identify the CMS running on 10.10.10.10

**Category:** CMS fingerprint | **Tools:** [whatweb](../../tools/whatweb.md), [wappalyzer](../../tools/wappalyzer.md)

**Steps:**
1. `whatweb -a 3 http://10.10.10.10` ← [why -a 3](../../tools/whatweb.md#aggression)
2. Look for `WordPress`, `Joomla`, `Drupal`, `Magento` tags.

**Answer format:** CMS name + version (e.g. `WordPress 5.8.2`)

---

### Q4 🟢 — Detect a WAF in front of 10.10.10.10

**Category:** WAF fingerprint | **Tools:** [wafw00f](../../tools/wafw00f.md)

**Steps:**
1. `wafw00f http://10.10.10.10` ← [why](../../tools/wafw00f.md#basic-usage)
2. For verbose fingerprinting: `wafw00f -v http://10.10.10.10`

**Answer format:** WAF product name (e.g. `Cloudflare`, `ModSecurity`) or `No WAF detected`

**Gotcha:** wafw00f uses signatures — false negatives are common. Also try sending a known bad payload (`?id=1' OR 1=1--`) and watching for `403` / block page.

---

### Q5 🟢 — Find the default Apache welcome page on 10.10.10.5

**Category:** Default pages | **Tools:** `curl`, [nikto](../../tools/nikto.md)

**Steps:**
1. `curl -s http://10.10.10.5/ | grep -i "it works\|apache2"`
2. `nikto -h http://10.10.10.5` — nikto explicitly flags default install pages.

**Answer format:** Yes / No, or the title string (e.g. `Apache2 Ubuntu Default Page`)

---

### Q6 🟡 — Find the hidden admin panel on 10.10.10.15

**Category:** Dir brute | **Tools:** [gobuster](../../tools/gobuster.md), [dirb](../../tools/dirb.md), [ffuf](../../tools/ffuf.md)

**Steps:**
1. `gobuster dir -u http://10.10.10.15 -w /usr/share/wordlists/dirb/common.txt -t 50` ← [why -t 50](../../tools/gobuster.md#threads)
2. Or: `ffuf -u http://10.10.10.15/FUZZ -w /usr/share/wordlists/dirb/common.txt` ← [why FUZZ](../../tools/ffuf.md#fuzz-keyword)
3. Or: `dirb http://10.10.10.15`
4. Look for `200`, `301`, `302` on paths like `/admin`, `/manager`, `/login`, `/phpmyadmin`.

**Answer format:** path verbatim (e.g. `/admin/login.php`)

**Gotcha:** soft-404s — a lot of apps return `200 OK` for non-existent paths. Filter with `-mc 200,301,302,401,403` or `--fs <size>`.

---

### Q7 🟡 — Find a `/backup` directory on 10.10.10.15

**Category:** Dir brute | **Tools:** [gobuster](../../tools/gobuster.md), [ffuf](../../tools/ffuf.md)

**Steps:**
1. Brute with extensions:
   ```bash
   gobuster dir -u http://10.10.10.15 -w /usr/share/wordlists/dirb/common.txt \
     -x zip,tar,gz,bak,sql,old
   ```
   ← [why -x](../../tools/gobuster.md#extensions)
2. If found, download: `curl -O http://10.10.10.15/backup.zip`

**Answer format:** filename (e.g. `backup.zip`) or contents (creds inside)

**Gotcha:** also brute common filenames: `backup`, `backups`, `bak`, `old`, `site.bak`, `db.sql`, `wp-config.php.bak`.

---

### Q8 🟡 — Check if HTTP PUT method is allowed on 10.10.10.20

**Category:** Method abuse | **Tools:** `curl`, [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `curl -sX OPTIONS http://10.10.10.20 -i | grep -i allow`
2. Or: `nmap -p 80 --script http-methods --script-args http-methods.test-all 10.10.10.20`
3. If `PUT` listed, upload a shell:
   ```bash
   curl -X PUT http://10.10.10.20/shell.php --data-binary @shell.php
   ```

**Answer format:** Yes / No, and the list of allowed methods

**Gotcha:** methods in the `Allow:` header may lie — nmap's `http-methods --script-args http-methods.test-all` actually tests each verb.

---

### Q9 🟡 — Find directory listing enabled on 10.10.10.20

**Category:** Info disclosure | **Tools:** [nikto](../../tools/nikto.md), `curl`

**Steps:**
1. `nikto -h http://10.10.10.20 | grep -i "indexing"`
2. Manual probe: `curl -s http://10.10.10.20/images/ | grep -i "Index of"`
3. Walk interesting dirs: `/uploads`, `/backup`, `/images`, `/files`.

**Answer format:** the path where listing is enabled (e.g. `/uploads/`) or a filename exposed

**Gotcha:** Apache prints `<title>Index of /path</title>`, nginx prints `autoindex on` formatted differently — match both.

---

### Q10 🟡 — Identify technologies on 10.10.10.25 with whatweb

**Category:** Fingerprint | **Tools:** [whatweb](../../tools/whatweb.md)

**Steps:**
1. `whatweb -a 3 -v http://10.10.10.25` ← [why -v](../../tools/whatweb.md#verbose)
2. Note `HTTPServer`, `X-Powered-By`, `Cookies`, `Script`, `JQuery`, `PHP`.

**Answer format:** tech name + version (e.g. `PHP/7.4.3`, `jQuery 1.11.1`)

**Gotcha:** `-a 3` is aggressive and makes extra requests. Use `-a 1` if you suspect a WAF.

---

### Q11 🔴 — Virtual-host brute force on 10.10.10.30

**Category:** vhost enum | **Tools:** [gobuster](../../tools/gobuster.md), [ffuf](../../tools/ffuf.md)

**Steps:**
1. Baseline: `curl -sI http://10.10.10.30` and note response size.
2. gobuster vhost mode:
   ```bash
   gobuster vhost -u http://10.10.10.30 -w /usr/share/wordlists/subdomains.txt
   ```
   ← [why vhost](../../tools/gobuster.md#vhost-mode)
3. Or ffuf with Host header:
   ```bash
   ffuf -u http://10.10.10.30 -H "Host: FUZZ.example.com" \
        -w subdomains.txt -fs <baseline_size>
   ```

**Answer format:** vhost name (e.g. `admin.example.com`)

**Gotcha:** you MUST filter out the baseline size (`-fs`) or every response looks like a hit.

---

### Q12 🔴 — Recursive deep content discovery on 10.10.10.30

**Category:** Dir brute (deep) | **Tools:** [ffuf](../../tools/ffuf.md), [gobuster](../../tools/gobuster.md)

**Steps:**
1. First pass wide and fast:
   ```bash
   ffuf -u http://10.10.10.30/FUZZ -w /usr/share/wordlists/dirb/big.txt \
        -mc 200,301,302,401,403 -o pass1.json
   ```
2. Recurse into each 301: `-recursion -recursion-depth 2` ← [why](../../tools/ffuf.md#recursion)
3. For each found dir, brute files with extensions:
   ```bash
   ffuf -u http://10.10.10.30/admin/FUZZ -w raft-small-files.txt \
        -e .php,.bak,.txt,.zip
   ```
4. Target the admin / upload / api endpoints found.

**Answer format:** hidden file or endpoint (e.g. `/admin/config.php.bak`)

**Gotcha:** recursion will blow up the wordlist fast — cap depth at 2 and narrow the inner wordlist. Always `-mc` filter and `-fs` / `-fw` tune out soft-404s.

---

## Quick links

- [gobuster reference](../../tools/gobuster.md)
- [ffuf reference](../../tools/ffuf.md)
- [nikto reference](../../tools/nikto.md)
- [whatweb reference](../../tools/whatweb.md)
- [wafw00f reference](../../tools/wafw00f.md)
- [Web server playbook (decision tree)](../../playbooks/web-servers-playbook.md)
- [11-web-servers README](../../11-web-servers/README.md)
- [Related: 04-vuln-analysis](04-vuln-analysis.md) · [Related: 12-web-apps](12-web-apps.md)
