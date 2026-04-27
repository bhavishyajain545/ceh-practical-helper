# Q201 — Detect ARP Spoofing in PCAP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Analyze `arp_spoof.pcap`. Detect ARP spoofing by finding duplicate IP-to-MAC mappings. Which IP address has two different MAC addresses? Report the spoofed IP and both MACs."

---

## 🎯 Flag Format

```
spoofed_ip=<ip>; real_mac=<mac>; fake_mac=<mac>
```

Example: `spoofed_ip=192.168.1.1; real_mac=aa:bb:cc:dd:ee:ff; fake_mac=11:22:33:44:55:66`

---

## 💡 Hints

**Hint 1**

Filter: `arp` → dekho kaunsa IP do alag MACs se reply kar raha hai.

**Hint 2**

`tshark -r arp_spoof.pcap -Y "arp.opcode==2" -T fields -e arp.src.proto_ipv4 -e arp.src.hw_mac`

---

## ✅ Solution

```bash
tshark -r arp_spoof.pcap -Y "arp.opcode==2" -T fields -e arp.src.proto_ipv4 -e arp.src.hw_mac | sort | uniq
```

Look for same IP with different MACs.

**Answer:** `spoofed_ip=192.168.52.2; real_mac=<gateway_mac>; fake_mac=<attacker_mac>`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 arp -w arp_spoof.pcap &
arpspoof -i eth0 -t 192.168.52.129 192.168.52.2 &
sleep 10 && kill %2 && kill %1
```

Report back: "Lab ready for Q201 — arp_spoof.pcap with ARP poisoning traffic ready"
