# 01 Recon & Footprinting — copy-paste commands

> Replace `<DOMAIN>` with e.g. `example.com`. Replace `<NS>` with a nameserver IP or hostname.

## WHOIS — registrar / registrant

```bash
whois <DOMAIN>                              # full record
whois <DOMAIN> | grep -iE "registrar|creation|expir|name server|registrant"
whois <IP>                                  # works on IPs too (ARIN/RIPE/APNIC)
```

→ See [whois.md](../tools/whois.md)

## DNS lookups — dig / host / nslookup

```bash
# The "give me everything" one-liner
dig <DOMAIN> ANY +noall +answer

# Specific record types
dig <DOMAIN> A
dig <DOMAIN> MX
dig <DOMAIN> NS
dig <DOMAIN> TXT
dig <DOMAIN> SOA

# Use a specific resolver
dig @8.8.8.8 <DOMAIN>

# host / nslookup fallbacks
host -t mx <DOMAIN>
nslookup -type=mx <DOMAIN>
```

→ See [dig.md](../tools/dig.md) • [host.md](../tools/host.md) • [nslookup.md](../tools/nslookup.md)

## Zone transfer (AXFR)

```bash
# 1. Find the nameservers
dig <DOMAIN> NS +short

# 2. Ask each NS for the zone
dig axfr @<NS> <DOMAIN>

# Automated
dnsrecon -d <DOMAIN> -t axfr
```

→ See [dnsrecon.md → axfr](../tools/dnsrecon.md#axfr)

## Subdomain enumeration

```bash
# Sublist3r — fast, passive, multi-engine
sublist3r -d <DOMAIN> -o subs.txt

# dnsrecon bruteforce (needs wordlist)
dnsrecon -d <DOMAIN> -t brt -D /usr/share/wordlists/dnsmap.txt

# Google dork (paste into browser)
site:<DOMAIN> -www
```

→ See [sublist3r.md](../tools/sublist3r.md) • [dnsrecon.md](../tools/dnsrecon.md)

## Emails / hosts / OSINT — theHarvester

```bash
# All public sources
theHarvester -d <DOMAIN> -b all -f report.html

# Specific source (faster)
theHarvester -d <DOMAIN> -b bing -l 500
theHarvester -d <DOMAIN> -b crtsh          # SSL cert transparency → subdomains
```

→ See [theHarvester.md](../tools/theharvester.md)

## Reverse DNS on a range

```bash
dnsrecon -r 10.10.10.0/24
```

## recon-ng (scriptable OSINT)

```bash
recon-ng
> marketplace install all
> workspaces create <DOMAIN>
> modules load recon/domains-hosts/hackertarget
> options set SOURCE <DOMAIN>
> run
```

→ See [recon-ng.md](../tools/recon-ng.md)

## Shodan — internet-wide search

```bash
# CLI (after shodan init <APIKEY>)
shodan search 'org:"<COMPANY>" port:443'
shodan host <IP>
shodan domain <DOMAIN>
```

Or via web: `https://www.shodan.io/search?query=...`

→ See [shodan.md](../tools/shodan.md)

## Google dorks — the big 8

```
site:<DOMAIN>                 # limit to a site
site:<DOMAIN> -www            # non-www subdomains
filetype:pdf site:<DOMAIN>    # leaked docs
inurl:admin site:<DOMAIN>     # admin panels
intitle:"index of" <DOMAIN>   # open directories
"<COMPANY>" filetype:xls
cache:<DOMAIN>                # google's cached copy
link:<DOMAIN>                 # sites linking in
```

→ See [google-dorks.md](../tools/google-dorks.md)

## Output — save everything

```bash
mkdir -p recon && cd recon
whois <DOMAIN>               > whois.txt
dig <DOMAIN> ANY +noall +answer > dns.txt
sublist3r -d <DOMAIN> -o subs.txt
theHarvester -d <DOMAIN> -b all -f harvester
```
