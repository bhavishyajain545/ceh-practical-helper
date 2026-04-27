# Q207 — Find DHCP Assigned IP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Open `dhcp_traffic.pcap`. Find the DHCP Discover → Offer → Request → ACK sequence. What IP was assigned to the client? What is the DHCP server IP?"

---

## 🎯 Flag Format

```
assigned_ip=<ip>; dhcp_server=<ip>
```

---

## 💡 Hints

**Hint 1**

Filter: `dhcp` ya `bootp`

**Hint 2**

DHCP Offer mein `Your (Client) IP Address` field mein assigned IP hota hai.

---

## ✅ Solution

```bash
tshark -r dhcp_traffic.pcap -Y "dhcp.type==2" -T fields -e dhcp.ip.your -e ip.src
```

**Answer:** `assigned_ip=192.168.52.150; dhcp_server=192.168.52.1`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 port 67 or port 68 -w dhcp_traffic.pcap &
dhclient -r eth0 && dhclient eth0
sleep 5 && kill %1
```

Report back: "Lab ready for Q207 — dhcp_traffic.pcap with DHCP handshake ready"
