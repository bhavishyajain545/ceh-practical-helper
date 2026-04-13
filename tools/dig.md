# dig — DNS lookup utility

> **The DNS query tool.** Faster to type than `nslookup`, gives full records. Used for A/MX/NS/TXT/SOA lookups, zone transfers (AXFR), and reverse PTRs.

**Install check (Parrot — already installed):** `dig -v`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to find... | Run this | Why |
|---|---|---|
| **A record** (IP for name) | `dig example.com` | Default is A |
| All records | `dig example.com ANY` | One shot (many servers block) |
| **Mail servers** | `dig example.com MX` | For SMTP enum |
| **Nameservers** | `dig example.com NS` | Who to query next |
| **TXT records** (SPF/DMARC/verif) | `dig example.com TXT` | Often has secrets |
| **SOA** | `dig example.com SOA` | Serial / primary NS |
| **CNAME** | `dig www.example.com CNAME` | Alias target |
| **Reverse (PTR)** | `dig -x 8.8.8.8` | IP → name |
| **Zone transfer (AXFR)** | `dig @ns1.example.com example.com AXFR` | The money shot |
| **Query specific server** | `dig @1.1.1.1 example.com` | Bypass local DNS |
| Short output only | `dig +short example.com` | Just the answer |
| No extra info | `dig +noall +answer example.com` | Clean |
| Trace full resolution | `dig +trace example.com` | Root → TLD → authoritative |

---

## 🔑 Flags / query syntax

```
dig [@server] [name] [type] [+options]
```

| Option | Meaning |
|---|---|
| `@<server>` | **Query this server** instead of `/etc/resolv.conf` |
| `-x <IP>` | Reverse lookup |
| `-p <port>` | Non-standard DNS port |
| `-4` / `-6` | Force IPv4 / IPv6 |
| `+short` | Concise answer |
| `+noall +answer` | Only ANSWER section |
| `+trace` | Iterative resolution |
| `+tcp` | Force TCP (AXFR needs TCP) |
| `+dnssec` | Include DNSSEC records |
| `+nocmd +nostats` | Quieter output |

### Record types worth knowing
`A`, `AAAA`, `MX`, `NS`, `TXT`, `SOA`, `CNAME`, `PTR`, `SRV`, `AXFR`, `ANY`, `CAA`, `DNSKEY`.

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Basic A record
dig example.com +short

# 2. All the big ones in one go
for t in A AAAA MX NS TXT SOA; do dig +short example.com $t | sed "s/^/$t: /"; done

# 3. Find authoritative NS then try AXFR on each
NS=$(dig +short example.com NS)
for ns in $NS; do
  echo "=== $ns ==="
  dig @$ns example.com AXFR
done

# 4. Reverse lookup a /24 range
for i in $(seq 1 254); do dig +short -x 10.10.10.$i; done

# 5. SPF / DMARC / domain verification strings
dig example.com TXT +short
dig _dmarc.example.com TXT +short

# 6. SRV record (common service discovery)
dig _ldap._tcp.dc._msdcs.example.com SRV +short

# 7. Query via a specific DNS server (target's internal DNS)
dig @<target-dns-IP> internal.example.com

# 8. Trace full resolution path
dig +trace example.com
```

---

## ⚠️ Gotchas

- **DNS server selection is critical.** By default `dig` uses `/etc/resolv.conf`. On the exam network you often need `@<target-IP>` to hit the internal DNS.
- **AXFR almost always fails on real servers** but CEH labs love it — always try against each NS.
- **AXFR uses TCP/53** — if blocked you'll see a timeout, not a refuse.
- **`ANY` queries** are deprecated (RFC 8482) — many resolvers return HINFO or nothing. Query each type explicitly.
- **`+short`** is great for scripting but hides errors; drop it if you're debugging.
- **Reverse DNS** (`-x`) only works if PTR records exist — internal ranges often have them, public often doesn't.
- **TXT records** commonly leak cloud services, mail providers, ownership proofs — always read them.
- **nslookup** exists but `dig` is clearer — use `host` for quick one-liners.

---

## 🔗 Related

- [dnsrecon](./dnsrecon.md) — automates bulk DNS enum
- [theharvester](./theharvester.md) — subdomain harvest via OSINT
- [whois](./whois.md) — domain ownership
- [nmap](./nmap.md) — `--script dns-zone-transfer`
- `fierce`, `dnsenum` — alternative DNS enum
