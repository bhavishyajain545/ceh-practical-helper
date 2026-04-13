# 🌐 Network Tools (Online)

> Port scan, traceroute, ping, IP info — sab browser se. Useful jab nmap install nahi ho ya target outside lab ho.

## Port scanning (online)

| Tool | URL | Use |
|---|---|---|
| **Pentest-tools Port Scanner** | https://pentest-tools.com/network-vulnerability-scanning/tcp-port-scanner-online-nmap | Free TCP scan via web |
| **MXToolbox Port Test** | https://mxtoolbox.com/PortScan.aspx | Single port check |
| **Hacker Target** | https://hackertarget.com/nmap-online-port-scanner/ | Nmap top 100 free |
| **You Get Signal** | https://www.yougetsignal.com/tools/open-ports/ | Quick port check |
| **DNS Checker Port** | https://dnschecker.org/port-scanner.php | Free, fast |

**Note:** Online scanners are slower and limited. For CEH lab targets (10.10.10.X), local nmap always faster — use online ones for *external* targets only.

---

## Traceroute / latency

| Tool | URL | Use |
|---|---|---|
| **Hacker Target Traceroute** | https://hackertarget.com/online-traceroute/ | Free traceroute |
| **WhatsMyIP Traceroute** | https://whatsmyip.org/ip-tools/traceroute/ | Alternative |
| **MTR Online** | https://mtr.sh/ | Multi-source traceroute |

---

## Ping

| Tool | URL | Use |
|---|---|---|
| **Site24x7 Ping** | https://www.site24x7.com/ping-test.html | Multi-location ping |
| **Ping.eu** | https://ping.eu/ping/ | EU-based ping |

---

## DNS / Reverse DNS

| Tool | URL | Use |
|---|---|---|
| **MXToolbox** | https://mxtoolbox.com/ | All DNS lookups in one place |
| **DNSlytics** | https://dnslytics.com/ | DNS + IP intelligence |
| **DNS Checker** | https://dnschecker.org/ | Global DNS propagation |
| **ViewDNS Reverse IP** | https://viewdns.info/reverseip/ | Find all domains on an IP |

**Reverse IP** is useful: same IP = same server = potentially other vhosts to attack.

---

## BGP / ASN / IP ranges

| Tool | URL | Use |
|---|---|---|
| **BGP.HE.net** | https://bgp.he.net/ | ASN lookup, IP range, peer info — Hurricane Electric |
| **BGPview** | https://bgpview.io/ | Cleaner UI, same data |
| **PeeringDB** | https://www.peeringdb.com/ | Network info |

**Use case:** Tumhe target ki sari IP ranges chahiye. Search ASN → see all owned blocks.

---

## Whois IP

| Tool | URL | Use |
|---|---|---|
| **ipinfo.io** | https://ipinfo.io/ | Geo + ISP + ASN, simple |
| **DB-IP.com** | https://db-ip.com/ | Geo + DNS + RDAP |
| **ARIN** | https://www.arin.net/ | Americas RIR |
| **RIPE** | https://www.ripe.net/ | Europe RIR |
| **APNIC** | https://www.apnic.net/ | Asia-Pacific RIR |

---

## Email server testing

| Tool | URL | Use |
|---|---|---|
| **MXToolbox SMTP Test** | https://mxtoolbox.com/diagnostic.aspx | SMTP connect, banner, blacklist |
| **DNS Checker SMTP** | https://dnschecker.org/smtp-test.php | SMTP server test |

---

## Internet routing visibility

| Tool | URL | Use |
|---|---|---|
| **RIPEstat** | https://stat.ripe.net/ | Detailed BGP/IP stats |
| **CAIDA** | https://www.caida.org/ | Network research data |

---

## ⚠️ Gotchas

- **Online scanners** are slow and rate-limited
- **Never scan random IPs** without authorization — these tools do scan from third-party servers, some log
- **For CEH lab IPs (10.x)** these online tools won't reach the lab — use local nmap
- **For exam:** these are mostly bookmarks for *general knowledge* during prep, not in-exam tools
