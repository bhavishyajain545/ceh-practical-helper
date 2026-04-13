# nslookup — simple DNS query tool

> **The "is there a DNS record?" tool.** Less powerful than dig but universally available (Windows + Linux).

**Install check:** `nslookup` (bind-utils / dnsutils)

---

## 🎯 Cheat-flow

```bash
nslookup target.com                          # A record via system resolver
nslookup target.com 8.8.8.8                  # query specific server
nslookup -type=MX target.com                 # mail servers
nslookup -type=NS target.com                 # nameservers
nslookup -type=TXT target.com                # SPF/DKIM/DMARC
nslookup -type=SOA target.com
nslookup -type=ANY target.com                # often blocked
nslookup 1.2.3.4                             # PTR (reverse)
```

Interactive mode:
```text
nslookup
> server 8.8.8.8
> set type=MX
> target.com
> exit
```

---

## 🔑 Key options

| Option | Meaning |
|---|---|
| `-type=<RR>` | A / AAAA / MX / NS / TXT / SOA / CNAME / PTR / SRV / ANY |
| `-port=<N>` | Use non-standard port |
| `-timeout=<N>` | Seconds |
| `-retry=<N>` | Retry count |
| `-debug` | Verbose |

---

## 📋 Recipes

```bash
# 1. Where does mail go?
nslookup -type=MX target.com

# 2. Who runs DNS?
nslookup -type=NS target.com

# 3. Reverse for an IP
nslookup 8.8.8.8

# 4. Attempt zone transfer (usually blocked — use dig -t axfr)
nslookup
> server ns1.target.com
> set type=AXFR
> target.com
```

---

## ⚠️ Gotchas

- `-type=ANY` has been deprecated by most resolvers; returns HINFO or partial data.
- Zone transfer (`AXFR`) is easier with `dig -t axfr target.com @ns1.target.com` — see [dig](dig.md).
- nslookup's error output is terse — use dig for troubleshooting.
- Windows vs Linux nslookup behave slightly differently — same flags though.

---

## 🔗 Related

- [dig](dig.md) · [host](host.md) · [dnsrecon](dnsrecon.md) · [whois](whois.md)
