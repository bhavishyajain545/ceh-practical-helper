# Question Bank — Reconnaissance (12 questions)

> Realistic CEH-Practical-style passive/OSINT questions. Steps link into individual tool docs — click any command to see the full reference.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Find the registrar of example.com

**Category:** WHOIS | **Tools:** [whois](../../tools/whois.md)

**Steps:**
1. `whois example.com` ← [why whois](../../tools/whois.md#basic-usage)
2. Grep the output: `whois example.com | grep -i "registrar:"`

**Answer format:** registrar name verbatim (e.g. `GoDaddy.com, LLC`)

**Gotcha:** some TLDs (`.io`, `.co`) return minimal data — fall back to the TLD's RDAP service if whois is empty.

---

### Q2 🟢 — What are the name servers for example.com?

**Category:** WHOIS / DNS | **Tools:** [whois](../../tools/whois.md), [dig](../../tools/dig.md)

**Steps:**
1. `whois example.com | grep -i "name server"` ← [why](../../tools/whois.md#basic-usage)
2. Or query DNS directly: `dig NS example.com +short` ← [why NS](../../tools/dig.md#record-types)

**Answer format:** FQDN(s) of NS records (e.g. `ns1.example.com`)

**Gotcha:** whois may list them uppercase — the exam usually accepts either case.

---

### Q3 🟢 — When does the example.com domain expire?

**Category:** WHOIS | **Tools:** [whois](../../tools/whois.md)

**Steps:**
1. `whois example.com | grep -i "expir"` ← [why](../../tools/whois.md#basic-usage)

**Answer format:** date in the format shown (usually `YYYY-MM-DD`)

---

### Q4 🟢 — Find the mail servers (MX) for example.com

**Category:** DNS enum | **Tools:** [dig](../../tools/dig.md)

**Steps:**
1. `dig MX example.com +short` ← [why MX](../../tools/dig.md#record-types)
2. Lowest priority number = primary mail server.

**Answer format:** FQDN of MX record (e.g. `mail.example.com`)

**Gotcha:** if the answer wants the hostname, strip the trailing dot and priority number.

---

### Q5 🟡 — Read the SPF record for example.com

**Category:** DNS enum | **Tools:** [dig](../../tools/dig.md)

**Steps:**
1. `dig TXT example.com +short` ← [why TXT](../../tools/dig.md#record-types)
2. Look for the string starting with `v=spf1`.

**Answer format:** the full SPF string (e.g. `v=spf1 include:_spf.google.com ~all`)

**Gotcha:** also check `_dmarc.example.com` TXT and `default._domainkey.example.com` if they ask about DMARC/DKIM.

---

### Q6 🟡 — Enumerate subdomains of example.com

**Category:** Subdomain enum | **Tools:** [sublist3r](../../tools/sublist3r.md), [theharvester](../../tools/theharvester.md)

**Steps:**
1. `sublist3r -d example.com -o subs.txt` ← [why](../../tools/sublist3r.md#basic-usage)
2. Cross-check with theHarvester: `theHarvester -d example.com -b all -f harv.html` ← [why -b all](../../tools/theharvester.md#sources)
3. Count unique hosts: `sort -u subs.txt | wc -l`

**Answer format:** integer count or specific subdomain name

**Gotcha:** some sources need API keys — `-b crtsh,bing,duckduckgo` is key-free.

---

### Q7 🟡 — Attempt a DNS zone transfer against example.com

**Category:** DNS enum | **Tools:** [dig](../../tools/dig.md), [dnsrecon](../../tools/dnsrecon.md)

**Steps:**
1. List NS first: `dig NS example.com +short`
2. Try AXFR against each: `dig AXFR example.com @ns1.example.com` ← [why AXFR](../../tools/dig.md#zone-transfer)
3. Or use dnsrecon: `dnsrecon -d example.com -t axfr` ← [why](../../tools/dnsrecon.md#zone-transfer)

**Answer format:** count of records, or a specific subdomain/IP from the transfer

**Gotcha:** AXFR almost always fails in the wild — if the exam asks, it's deliberately enabled. Don't give up after one NS; try them all.

---

### Q8 🟡 — Harvest email addresses for example.com

**Category:** OSINT | **Tools:** [theharvester](../../tools/theharvester.md)

**Steps:**
1. `theHarvester -d example.com -b bing,duckduckgo,crtsh -l 500` ← [why -b](../../tools/theharvester.md#sources)
2. Look under the `[*] Emails found:` section.

**Answer format:** email address verbatim (e.g. `admin@example.com`) or integer count

**Gotcha:** `-b all` can hang on rate-limited sources — specify sources explicitly.

---

### Q9 🟡 — Find hosts exposing a specific banner via Shodan

**Category:** OSINT | **Tools:** [shodan](../../tools/shodan.md)

**Steps:**
1. Web UI: `https://www.shodan.io/search?query=apache+country:US`
2. CLI: `shodan search "apache country:US" --fields ip_str,port,org` ← [why filters](../../tools/shodan.md#search-filters)
3. For a known host: `shodan host 10.10.10.1`

**Answer format:** IP address, org name, or integer count (depends on Q)

**Gotcha:** Shodan requires an API key (`shodan init <key>`). Useful filters: `port:`, `org:`, `hostname:`, `product:`, `version:`.

---

### Q10 🔴 — Google-dork to find exposed config files for example.com

**Category:** OSINT | **Tools:** Google, `curl`

**Steps:**
1. `site:example.com filetype:env` — exposed `.env` files
2. `site:example.com filetype:sql` — SQL dumps
3. `site:example.com intitle:"index of"` — dir listings
4. `site:example.com inurl:admin` — admin panels
5. Fetch any hit: `curl -s https://example.com/path/.env`

**Answer format:** the exposed filename, credential, or URL

**Gotcha:** memorize the 5 big operators: `site:`, `filetype:`, `intitle:`, `inurl:`, `intext:`. Combine them.

---

### Q11 🔴 — Full DNS brute-force of subdomains (wordlist-based)

**Category:** DNS enum | **Tools:** [dnsrecon](../../tools/dnsrecon.md)

**Steps:**
1. `dnsrecon -d example.com -D /usr/share/wordlists/dnsmap.txt -t brt` ← [why -t brt](../../tools/dnsrecon.md#brute-force)
2. Or: `dnsrecon -d example.com -t std` for a standard record sweep first.
3. Save output: add `-j out.json`.

**Answer format:** subdomain hostname or integer count

**Gotcha:** wildcard DNS (`*.example.com → same IP`) produces false positives — dnsrecon detects and warns about this; note the warning.

---

### Q12 🔴 — Identify the hosting provider and ASN of example.com

**Category:** OSINT | **Tools:** [whois](../../tools/whois.md), [dig](../../tools/dig.md)

**Steps:**
1. Resolve IP: `dig A example.com +short`
2. Whois on the IP: `whois <IP> | grep -iE "orgname|netname|origin"` ← [why](../../tools/whois.md#ip-whois)
3. Or use Team Cymru: `whois -h whois.cymru.com " -v <IP>"`

**Answer format:** ASN number (e.g. `AS15169`) or organization name

**Gotcha:** Cloudflare/Akamai-fronted sites will return the CDN ASN, not the origin — note this caveat on exam answers.

---

## Quick links

- [whois reference](../../tools/whois.md)
- [dig reference](../../tools/dig.md)
- [theHarvester reference](../../tools/theharvester.md)
- [sublist3r reference](../../tools/sublist3r.md)
- [dnsrecon reference](../../tools/dnsrecon.md)
- [Recon playbook (decision tree)](../../playbooks/recon-playbook.md)
- [01-recon README](../../01-recon/README.md)
- [Next: 02-scanning](02-scanning.md)
