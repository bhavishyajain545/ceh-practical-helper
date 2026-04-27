# Q197 — Find DNS Queries in PCAP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Open `dns_traffic.pcap`. Find all DNS queries made. What domain was queried most frequently? What is the resolved IP for `secret.ceh-lab.local`?"

---

## 🎯 Flag Format

```
domain=<name>; resolved_ip=<ip>
```

Example: `domain=example.com; resolved_ip=93.184.216.34`

---

## 💡 Hints

**Hint 1**

Filter: `dns.qry.name` — DNS query names dikhenge.

**Hint 2**

`tshark -r dns_traffic.pcap -Y "dns.qry.name" -T fields -e dns.qry.name | sort | uniq -c | sort -rn`

---

## ✅ Solution

```bash
tshark -r dns_traffic.pcap -Y "dns.qry.name" -T fields -e dns.qry.name | sort | uniq -c | sort -rn | head -5
tshark -r dns_traffic.pcap -Y "dns.qry.name==secret.ceh-lab.local" -T fields -e dns.a
```

**Answer:** `domain=secret.ceh-lab.local; resolved_ip=192.168.52.200`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 port 53 -w dns_traffic.pcap &
nslookup secret.ceh-lab.local 192.168.52.129
dig secret.ceh-lab.local @192.168.52.129
sleep 2 && kill %1
```

Report back: "Lab ready for Q197 — dns_traffic.pcap with DNS queries ready"
