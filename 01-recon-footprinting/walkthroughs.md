# 01 Recon & Footprinting — full walkthroughs

End-to-end examples. Start with a domain, end with an answer.

---

## Walkthrough 1: "Who is the registrar of certifiedhacker.com?"

**Type:** whois | **Difficulty:** easy | **Time:** 30 sec

1. Straight `whois`:
   ```bash
   whois certifiedhacker.com
   ```
2. Grep for the exact field ([why this field](../tools/whois.md#fields)):
   ```bash
   whois certifiedhacker.com | grep -i "Registrar:"
   ```
3. Example output:
   ```
   Registrar: NameCheap, Inc.
   ```
4. **Answer:** `NameCheap, Inc.` (verbatim, including the comma)

**Gotcha:** the question asks for **Registrar** not **Registrant**. Different fields. Registrant = who owns it, Registrar = who sold it.

---

## Walkthrough 2: "Find all subdomains of example.com"

**Type:** subdomain enum | **Difficulty:** medium | **Time:** 3–5 min

1. Start with passive enum (no packets to target):
   ```bash
   sublist3r -d example.com -o subs.txt
   ```
2. While it runs, try cert transparency via theHarvester ([why crtsh](../tools/theHarvester.md#sources)):
   ```bash
   theHarvester -d example.com -b crtsh
   ```
3. Cross-check with a Google dork in the browser:
   ```
   site:example.com -www
   ```
4. Merge and dedupe:
   ```bash
   cat subs.txt | sort -u > final.txt
   wc -l final.txt
   ```
5. **Answer:** the count, or the list — depends on wording.

**Gotcha:** if the question says "DNS bruteforce", use `dnsrecon -t brt`, not the passive sources — CEH sometimes scores the method, not just the answer.

---

## Walkthrough 3: "Perform a zone transfer on zonetransfer.me and return the host entries"

**Type:** AXFR | **Difficulty:** easy | **Time:** 1 min

1. Find the nameservers:
   ```bash
   dig zonetransfer.me NS +short
   ```
   Output:
   ```
   nsztm1.digi.ninja.
   nsztm2.digi.ninja.
   ```
2. Ask each one for the zone:
   ```bash
   dig axfr @nsztm1.digi.ninja zonetransfer.me
   ```
3. You should see dozens of records (A, MX, TXT, SRV…). Save it:
   ```bash
   dig axfr @nsztm1.digi.ninja zonetransfer.me > axfr.txt
   ```
4. **Answer:** count or list the hostnames, whichever the question asks for.

**Gotcha:** most real domains refuse AXFR (`; Transfer failed.`). When that happens, fall back to subdomain bruteforce — see walkthrough 2.

---

## Walkthrough 4: "Find 5 email addresses belonging to microsoft.com"

**Type:** OSINT | **Difficulty:** medium | **Time:** 3 min

1. Fire theHarvester across all sources:
   ```bash
   theHarvester -d microsoft.com -b all -l 500 -f ms
   ```
2. While it runs, open Google with:
   ```
   "@microsoft.com" filetype:pdf
   ```
3. Check the generated `ms.html` report — emails are in a dedicated section.
4. **Answer:** 5 valid `user@microsoft.com` addresses.

**Gotcha:**
- Some sources (google) rate-limit hard → use `-b bing,crtsh,duckduckgo` instead of `all`
- CEH sometimes asks for a specific count — match exactly, don't give extras
- API-gated sources (hunter, github) need keys in `api-keys.yaml`

→ Next step is usually enumerating those employees on LinkedIn / recon-ng: see [recon-ng.md](../tools/recon-ng.md)
