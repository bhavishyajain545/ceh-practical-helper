# Q192 — Filter Traffic by Source IP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Open `network.pcap` and filter all traffic from source IP `192.168.52.128`. How many packets originate from this IP? What protocols are used?"

---

## 🎯 Flag Format

```
packets=<number>; protocols=<list>
```

Example: `packets=45; protocols=TCP,UDP,ICMP`

---

## 💡 Hints

**Hint 1**

Display filter: `ip.src == 192.168.52.128`

**Hint 2**

`tshark -r network.pcap -Y "ip.src==192.168.52.128" | wc -l`

---

## ✅ Solution

```bash
tshark -r network.pcap -Y "ip.src==192.168.52.128" | wc -l
tshark -r network.pcap -Y "ip.src==192.168.52.128" -T fields -e frame.protocols | sort -u
```

**Answer:** `packets=<count>; protocols=TCP,HTTP`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 -w network.pcap -c 200 &
nmap -sT 192.168.52.129 -p 80,21,22
sleep 5 && kill %1
```

Report back: "Lab ready for Q192 — network.pcap with mixed traffic ready"
