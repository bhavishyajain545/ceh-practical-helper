# whois — domain / IP registration lookup

> **Footprinting basics.** Registrar, name servers, creation/expiry dates, abuse contacts, sometimes registrant email.

**Install check (Parrot — already installed):** `whois --version`

---

## 🎯 Cheat-flow

| You need... | Run this |
|---|---|
| **Domain info** | `whois example.com` |
| **IP owner** (ASN / netblock) | `whois 8.8.8.8` |
| AS number info | `whois AS15169` |
| Query a specific whois server | `whois -h whois.arin.net 8.8.8.8` |
| Save output | `whois example.com > whois.txt` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-h <server>` | Query a specific whois server |
| `-p <port>` | Alternate port |
| `-H` | Hide legal disclaimers |
| `-a` | Search all databases |
| `-I` | First query IANA (for TLD discovery) |

---

## 📋 Command recipes

```bash
# 1. Standard domain lookup
whois example.com

# 2. Just the juicy fields
whois example.com | grep -iE 'registrar|creation|expir|name server|registrant|email'

# 3. IP → netblock owner
whois 192.0.2.1

# 4. Pick your RIR directly
whois -h whois.arin.net 8.8.8.8        # North America
whois -h whois.ripe.net 80.80.80.80    # Europe/ME
whois -h whois.apnic.net 1.1.1.1       # Asia-Pacific
whois -h whois.lacnic.net 200.1.1.1    # Latin America
whois -h whois.afrinic.net 196.1.1.1   # Africa

# 5. Hide the legal boilerplate
whois -H example.com
```

---

## ⚠️ Gotchas

- **GDPR redaction:** EU domains and most `.com` records now hide registrant name/email. Don't expect it.
- **Rate limiting:** whois servers cut you off after ~20 queries/min. Space them out.
- **Correct RIR:** IP lookups route via IANA then to the right RIR automatically, but you can force it with `-h` for speed.
- **Registrar info** is often the most useful field for exam Qs — creation date, expiry date, name servers.
- **.io / .ai / weird TLDs** may need a direct `-h` to the sponsoring registry.

---

## 🔗 Related

- [dig](./dig.md) — resolve what you find
- [theharvester](./theharvester.md) — emails + subdomains
- [dnsrecon](./dnsrecon.md) — DNS enumeration
- [footprinting playbook](../playbooks/footprinting-playbook.md)
