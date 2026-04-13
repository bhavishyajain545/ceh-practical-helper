# host — concise DNS lookup

> **Smallest DNS client.** One-line answer for a hostname, IP, or record type.

**Install check:** `host -V` (bind-utils / dnsutils)

---

## 🎯 Cheat-flow

```bash
host target.com                    # A + AAAA + MX
host -t A target.com               # A only
host -t MX target.com              # mail servers
host -t NS target.com              # nameservers
host -t TXT target.com             # TXT (SPF/DMARC)
host -t SOA target.com
host -t ANY target.com
host -a target.com                 # verbose all records
host 8.8.8.8                       # reverse (PTR)
host target.com 1.1.1.1            # query specific server
host -l target.com ns1.target.com  # attempt AXFR zone transfer
```

---

## 🔑 Options

| Flag | Meaning |
|---|---|
| `-t <TYPE>` | Record type |
| `-a` | All records (same as `-v -t ANY`) |
| `-v` | Verbose |
| `-l` | List zone (AXFR) |
| `-T` | Use TCP |
| `-4` / `-6` | Force IPv4/6 |
| `-W <sec>` | Timeout |
| `-R <N>` | Retry count |

---

## 📋 Recipes

```bash
# 1. Zone transfer — great win if it works
host -l target.com ns1.target.com

# 2. Fast subdomain check from a wordlist
for s in $(cat subs.txt); do
  host "$s.target.com" | grep "has address"
done

# 3. Who handles mail
host -t MX target.com

# 4. Reverse lookup a netblock (simple loop)
for i in $(seq 1 254); do host 10.10.10.$i | grep -v NXDOMAIN; done
```

---

## ⚠️ Gotchas

- `host -l` rarely works against modern DNS servers — AXFR is usually restricted.
- For scripting / richer output use [dig](dig.md).
- Silent on NXDOMAIN when used in loops — grep filter needed.

---

## 🔗 Related

- [dig](dig.md) · [nslookup](nslookup.md) · [dnsrecon](dnsrecon.md)
