# Q199 — Detect SYN Scan in PCAP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Analyze `scan_traffic.pcap`. Detect if a SYN scan was performed. Identify the scanner's IP, number of ports scanned, and how many were open (SYN-ACK responses)."

---

## 🎯 Flag Format

```
scanner_ip=<ip>; ports_scanned=<number>; open_ports=<number>
```

Example: `scanner_ip=192.168.52.128; ports_scanned=1000; open_ports=23`

---

## 💡 Hints

**Hint 1**

SYN scan = bahut saare SYN packets ek IP se, bina 3-way handshake complete kiye.

**Hint 2**

Filter: `tcp.flags.syn==1 && tcp.flags.ack==0` — SYN packets count karo. `tcp.flags.syn==1 && tcp.flags.ack==1` — open ports.

---

## ✅ Solution

```bash
# Scanner IP (most SYN packets sender)
tshark -r scan_traffic.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" -T fields -e ip.src | sort | uniq -c | sort -rn | head -1
# Ports scanned
tshark -r scan_traffic.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" -T fields -e tcp.dstport | sort -u | wc -l
# Open ports (SYN-ACK)
tshark -r scan_traffic.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==1" -T fields -e tcp.srcport | sort -u | wc -l
```

**Answer:** `scanner_ip=192.168.52.128; ports_scanned=1000; open_ports=23`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 -w scan_traffic.pcap &
nmap -sS 192.168.52.129
sleep 10 && kill %1
```

Report back: "Lab ready for Q199 — scan_traffic.pcap with SYN scan traffic ready"
