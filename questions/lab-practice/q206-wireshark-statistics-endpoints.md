# Q206 — Find Top Talkers Using Statistics

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Open `network_dump.pcap`. Use Wireshark Statistics to find the top 3 IP endpoints by packet count. Which IP sent the most data?"

---

## 🎯 Flag Format

```
top_ip=<ip>; packets=<number>; bytes=<number>
```

---

## 💡 Hints

**Hint 1**

Wireshark: Statistics → Endpoints → IPv4 tab → Sort by Packets.

**Hint 2**

`tshark -r network_dump.pcap -q -z endpoints,ip`

---

## ✅ Solution

```bash
tshark -r network_dump.pcap -q -z endpoints,ip | sort -t'|' -k3 -rn | head -5
```

**Answer:** `top_ip=192.168.52.128; packets=<count>; bytes=<count>`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 -w network_dump.pcap -c 500 &
nmap -sV 192.168.52.129
curl http://192.168.52.129/
sleep 10 && kill %1
```

Report back: "Lab ready for Q206 — network_dump.pcap ready"
