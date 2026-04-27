# 📦 PCAP FILE DI HAI — Credentials/Flag Nikalo

---

## STEP 1: Pehle Overview Lo
```bash
# Basic info
tshark -r capture.pcap -q -z conv,ip          # top talkers
tshark -r capture.pcap -q -z endpoints,ip     # all endpoints
tshark -r capture.pcap -Y "frame" | wc -l     # total packets
```

---

## CASE 1: HTTP Credentials Dhundo
```bash
# Basic Auth (Base64)
tshark -r cap.pcap -Y "http.authorization" -T fields -e http.authorization
# Output: Basic YWRtaW46cGFzc3dvcmQ=
echo "YWRtaW46cGFzc3dvcmQ=" | base64 -d     # admin:password

# POST form data
tshark -r cap.pcap -Y "http.request.method==POST" -T fields -e urlencoded-form.key -e urlencoded-form.value

# Wireshark: Filter → http.authorization ya http.request.method==POST
```

---

## CASE 2: FTP Credentials (Plaintext!)
```bash
tshark -r cap.pcap -Y "ftp.request.command==USER || ftp.request.command==PASS" -T fields -e ftp.request.arg
# Wireshark: Filter → ftp → USER aur PASS packets dekho
```

---

## CASE 3: Telnet Session Reconstruct
```bash
tshark -r cap.pcap -z follow,tcp,ascii,0 -q
# Wireshark: Filter → telnet → Right-click → Follow TCP Stream
```

---

## CASE 4: DNS Queries Find Karo
```bash
tshark -r cap.pcap -Y "dns.qry.name" -T fields -e dns.qry.name | sort -u
tshark -r cap.pcap -Y "dns.qry.name contains secret" -T fields -e dns.qry.name -e dns.a
```

---

## CASE 5: SMTP Email Content
```bash
tshark -r cap.pcap -z follow,tcp,ascii,0 -q
# Filter: smtp → Follow TCP Stream → email body padhna
```

---

## CASE 6: Files Export Karo
```bash
# HTTP objects
tshark -r cap.pcap --export-objects http,exported/
# SMB objects
tshark -r cap.pcap --export-objects smb,exported/
# Wireshark: File → Export Objects → HTTP/SMB
```

---

## CASE 7: Attack Detection
```bash
# SYN Scan detect
tshark -r cap.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" -T fields -e ip.src | sort | uniq -c | sort -rn

# DoS/Flood detect
tshark -r cap.pcap -T fields -e ip.src | sort | uniq -c | sort -rn | head -5

# ARP Spoofing
tshark -r cap.pcap -Y "arp.opcode==2" -T fields -e arp.src.proto_ipv4 -e arp.src.hw_mac | sort | uniq

# ICMP exfiltration
tshark -r cap.pcap -Y "icmp.type==8" -T fields -e data.data | xxd -r -p
```

---

## CASE 8: TLS/SSL Certificate Info
```bash
tshark -r cap.pcap -Y "tls.handshake.type==11" -T fields -e x509sat.uTF8String
tshark -r cap.pcap -Y "tls.handshake.type==2" -T fields -e tls.handshake.ciphersuite
```

---

## CASE 9: Wireless Traffic
```bash
# Beacon frames → SSIDs
tshark -r cap.pcap -Y "wlan.fc.type_subtype==8" -T fields -e wlan.ssid | sort -u
```

---

## USEFUL WIRESHARK FILTERS:
| Kya dhundna hai | Filter |
|---|---|
| HTTP traffic | `http` |
| Specific IP | `ip.addr == 192.168.52.129` |
| POST requests | `http.request.method == POST` |
| FTP creds | `ftp.request.command == USER \|\| ftp.request.command == PASS` |
| DNS queries | `dns.qry.name` |
| TCP stream follow | Right-click → Follow → TCP Stream |
| SYN packets only | `tcp.flags.syn==1 && tcp.flags.ack==0` |
| ARP traffic | `arp` |
| Contains string | `frame contains "flag"` |

---

## QUICK DECISION:
```
PCAP file mili
  ├─ HTTP? → Authorization header / POST data
  ├─ FTP? → USER + PASS commands (plaintext)
  ├─ Telnet? → Follow TCP Stream (plaintext)
  ├─ DNS? → query names / resolved IPs
  ├─ SMTP? → Follow stream → email body
  ├─ Files download hue? → Export Objects
  ├─ Attack detect? → SYN count / ARP duplicates
  └─ Pata nahi? → Follow TCP Stream #0 se shuru karo
```
