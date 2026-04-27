# PCAP FILE DI HAI — Kya Karu?

---

## Pehle Samjho: PCAP Kya Hai?

PCAP = **Packet Capture**. Ye ek file hoti hai jisme network traffic record hota hai —
matlab kaunse packets gaye, kaunse aaye, kya data tha — sab kuch.

**Socho aise:** Jaise CCTV recording mein sab kuch record hota hai,
waise PCAP file mein network ki saari baat-cheet record hoti hai.

**File extensions:**
- `.pcap` — standard format (libpcap)
- `.pcapng` — newer format with extra metadata
- `.cap` — same as pcap

**Kya mil sakta hai PCAP mein?**
- Usernames aur passwords (HTTP, FTP, Telnet mein plaintext hote hain!)
- Files jo download/upload hui
- DNS queries (kaunsi websites visit hui)
- Emails (SMTP traffic)
- Attack evidence (scans, DoS, spoofing)

---

## STEP 1: Pehle Overview Lo — Samjho Kya Hai Andar

```bash
# Kitne packets hain?
tshark -r capture.pcap -q -z io,stat,0
# Ye summary dega — total packets, duration, etc.

# Kaun kaun baat kar raha hai? (Top talkers)
tshark -r capture.pcap -q -z conv,ip
# Output mein IP pairs dikhenge — kaunsa IP kisse baat kar raha hai
# Sabse zyada packets wala pair important hai

# Saare endpoints (unique IPs)
tshark -r capture.pcap -q -z endpoints,ip
# Har IP kitne packets bhej/receive kar raha hai

# Kaunse protocols hain?
tshark -r capture.pcap -q -z io,phs
# Protocol hierarchy — HTTP hai? FTP hai? DNS hai? Sab dikhega
```

**Wireshark mein:**
- File > Open > pcap file select karo
- Statistics > Conversations — top talkers
- Statistics > Protocol Hierarchy — kaunse protocols
- Statistics > Endpoints — unique IPs

> **Pehle protocol hierarchy dekho!** Isse pata chalega kya dhundna hai.
> HTTP hai toh creds dhundo, FTP hai toh plaintext passwords, DNS hai toh queries, etc.

---

## CASE 1: HTTP Credentials Dhundo

HTTP mein credentials do tarike se jaate hain:

### A) Basic Authentication (Base64 encoded header)
```bash
tshark -r cap.pcap -Y "http.authorization" -T fields -e http.authorization
# Output: Basic YWRtaW46cGFzc3dvcmQ=
# "Basic" ke baad jo Base64 string hai, wo decode karo:

echo "YWRtaW46cGFzc3dvcmQ=" | base64 -d
# Output: admin:password
# Format hota hai: username:password
```

**Wireshark mein:** Filter box mein `http.authorization` type karo.
Packet pe click karo → Expand "Hypertext Transfer Protocol" → Authorization header dikhega.
"Credentials" field mein decoded value bhi dikhta hai!

### B) POST Form Data (Login forms)
```bash
tshark -r cap.pcap -Y "http.request.method==POST" -T fields \
  -e http.host -e http.request.uri -e urlencoded-form.key -e urlencoded-form.value
# Output mein username, password fields ke values dikhenge
# Example: username  admin  password  secret123
```

**Wireshark mein:** Filter: `http.request.method == POST`
Packet pe click karo → Expand "HTML Form URL Encoded" → key-value pairs dikhenge.

> **Kyun plaintext?** HTTP encrypted nahi hota (HTTPS hota hai).
> Isliye HTTP mein sab kuch readable hai — credentials bhi!

---

## CASE 2: FTP Credentials (Completely Plaintext!)

FTP protocol mein username aur password **bilkul plaintext** jaate hain. Bahut easy hai dhundna.

```bash
tshark -r cap.pcap -Y "ftp.request.command==USER || ftp.request.command==PASS" \
  -T fields -e ftp.request.command -e ftp.request.arg
# Output:
# USER  admin
# PASS  password123
```

**Wireshark mein:** Filter: `ftp`
Packets mein dekho — `USER admin` aur `PASS password123` seedha readable hoga.

> **FTP ka response bhi dekho:** `230 Login successful` matlab credentials sahi the.
> `530 Login incorrect` matlab galat the — agle USER/PASS attempt dhundo.

---

## CASE 3: Telnet Session Reconstruct

Telnet bhi completely plaintext hai. Puri session reconstruct ho sakti hai.

```bash
# TCP stream follow karo (stream 0 se shuru karo)
tshark -r cap.pcap -z follow,tcp,ascii,0 -q
# Ye poori conversation dikhayega — login, commands, output sab

# Agar specific stream chahiye:
tshark -r cap.pcap -z follow,tcp,ascii,1 -q
# Stream number badal ke try karo (0, 1, 2, ...)
```

**Wireshark mein:** Filter: `telnet`
Kisi bhi packet pe right-click → Follow → TCP Stream
Ek naya window khulega jisme poori conversation dikhegi — username, password, commands sab.

> **Telnet mein ek baat dhyan rakho:** Har character separately bhejta hai.
> Toh `admin` as `a`, `d`, `m`, `i`, `n` dikhega packets mein.
> TCP stream follow karne se ye automatically combine ho jaata hai.

---

## CASE 4: DNS Queries Find Karo

DNS queries se pata chalta hai user kaunsi websites visit kar raha tha.

```bash
# Saari unique DNS queries
tshark -r cap.pcap -Y "dns.qry.name" -T fields -e dns.qry.name | sort -u
# Output: google.com, facebook.com, suspicious-site.com ...

# Specific keyword dhundo (flag, secret, etc.)
tshark -r cap.pcap -Y "dns.qry.name contains secret" \
  -T fields -e dns.qry.name -e dns.a
# -e dns.a = resolved IP address bhi dikhao

# DNS exfiltration detect karo (unusual long subdomains)
tshark -r cap.pcap -Y "dns.qry.name" -T fields -e dns.qry.name | awk 'length>50'
# 50+ chars ke domains suspicious hain — data exfiltration ho sakta hai
```

**Wireshark mein:** Filter: `dns`
Har query ka "Name" field dekho. Agar koi domain suspicious lagta hai — wo answer hai!

---

## CASE 5: SMTP Email Content Padhna

SMTP se emails jaate hain — unka content padh sakte ho.

```bash
# SMTP streams follow karo
tshark -r cap.pcap -Y "smtp" -z follow,tcp,ascii,0 -q
# Email headers (From, To, Subject) aur body sab dikhega

# Specific fields extract karo
tshark -r cap.pcap -Y "smtp.req.parameter contains FROM" \
  -T fields -e smtp.req.parameter
```

**Wireshark mein:** Filter: `smtp`
Right-click → Follow → TCP Stream
Pura email dikhega — FROM, TO, SUBJECT, aur body bhi.

> **Email mein attachment bhi ho sakta hai** — wo Base64 encoded hoga.
> Copy karke `base64 -d` se decode kar sakte ho.

---

## CASE 6: Files Export Karo (Downloaded Files Nikalo)

Agar koi file download/upload hui hai PCAP mein, toh extract kar sakte ho.

```bash
# HTTP se downloaded files nikalo
mkdir -p /tmp/exported
tshark -r cap.pcap --export-objects http,/tmp/exported/
ls /tmp/exported/
# Saari HTTP downloaded files aa jayengi yahan

# SMB (Windows file sharing) se files nikalo
tshark -r cap.pcap --export-objects smb,/tmp/exported_smb/

# DICOM (medical imaging) / IMF (email) / TFTP bhi kar sakte ho
tshark -r cap.pcap --export-objects tftp,/tmp/exported_tftp/
```

**Wireshark mein:** File → Export Objects → HTTP (ya SMB/DICOM)
Ek list aayegi saari files ki — Save All click karo.

> **Export ke baad files check karo:** `file *` se type dekho,
> `strings` se hidden text dhundo, images open karo — flag mil sakta hai!

---

## CASE 7: Attack Detection

### SYN Scan Detect (Port Scanning)
```bash
# SYN packets count by source IP
tshark -r cap.pcap -Y "tcp.flags.syn==1 && tcp.flags.ack==0" \
  -T fields -e ip.src | sort | uniq -c | sort -rn
# Agar ek IP se bahut saare SYN packets → wo scanner hai
# 100+ different ports pe SYN = Nmap SYN scan
```

**Output samjho:**
```
   1500  192.168.1.100
      5  192.168.1.1
```
1500 SYN packets ek IP se → ye attacker hai jo port scan kar raha hai.

### DoS / DDoS / Flood Attack
```bash
# Sabse zyada packets kis IP se aa rahe?
tshark -r cap.pcap -T fields -e ip.src | sort | uniq -c | sort -rn | head -5
# Abnormally high count = flood attack source

# SYN Flood specifically
tshark -r cap.pcap -Y "tcp.flags==0x002" -T fields -e ip.src | sort | uniq -c | sort -rn
```

### ARP Spoofing Detect
```bash
# ARP replies dekho — ek IP ke liye multiple MAC addresses = spoofing!
tshark -r cap.pcap -Y "arp.opcode==2" \
  -T fields -e arp.src.proto_ipv4 -e arp.src.hw_mac | sort | uniq
# Agar same IP ke liye 2 different MACs dikhein → ARP spoof!
```

### ICMP Data Exfiltration
```bash
# ICMP ping mein hidden data
tshark -r cap.pcap -Y "icmp.type==8" -T fields -e data.data | xxd -r -p
# Ping packets ke data portion mein secret data chhupa hota hai
```

---

## CASE 8: TLS/SSL Certificate Info

```bash
# Certificate details (CN, issuer, etc.)
tshark -r cap.pcap -Y "tls.handshake.type==11" -T fields -e x509sat.uTF8String
# Common Name, Organization wagairah dikhega

# Cipher suite used
tshark -r cap.pcap -Y "tls.handshake.type==2" -T fields -e tls.handshake.ciphersuite
# Ye batayega kaunsa encryption use hua

# Server Name Indication (SNI) — kaunsi website
tshark -r cap.pcap -Y "tls.handshake.extensions_server_name" \
  -T fields -e tls.handshake.extensions_server_name
```

---

## CASE 9: Wireless Traffic (WiFi PCAP)

```bash
# WiFi network names (SSIDs) — Beacon frames se
tshark -r cap.pcap -Y "wlan.fc.type_subtype==8" -T fields -e wlan.ssid | sort -u
# Saare nearby WiFi networks ke naam dikhenge

# Connected devices
tshark -r cap.pcap -Y "wlan.fc.type_subtype==0" -T fields -e wlan.sa -e wlan.ssid
```

---

## Useful Wireshark Display Filters — Yaad Rakho!

| Kya dhundna hai | Filter |
|---|---|
| Saara HTTP traffic | `http` |
| Specific IP ka traffic | `ip.addr == 192.168.1.100` |
| Source IP filter | `ip.src == 10.0.0.1` |
| Destination IP filter | `ip.dst == 10.0.0.1` |
| POST requests (login data) | `http.request.method == POST` |
| HTTP Basic Auth | `http.authorization` |
| FTP credentials | `ftp.request.command == USER \|\| ftp.request.command == PASS` |
| DNS queries | `dns.qry.name` |
| SYN packets (scan detect) | `tcp.flags.syn==1 && tcp.flags.ack==0` |
| ARP traffic | `arp` |
| Text search (kuch bhi dhundo) | `frame contains "flag"` |
| Specific port | `tcp.port == 80` |
| TCP stream follow | Right-click kisi packet pe → Follow → TCP Stream |
| HTTP response codes | `http.response.code == 200` |

---

## Common Mistakes (Mat Karna Ye)

1. **Protocol hierarchy nahi dekha** → Andhe ghode jaisa random filters try kar rahe ho
   - Pehle Statistics > Protocol Hierarchy dekho — kya hai andar!

2. **TCP Stream galat number follow kiya** → Ek stream mein kuch nahi mila
   - Multiple streams try karo: 0, 1, 2, 3... important data kisi bhi stream mein ho sakta hai

3. **HTTPS decrypt karna bhool gaye** → TLS traffic encrypted dikhega
   - Agar SSL key file di hai: Edit > Preferences > TLS > RSA Key list

4. **Export Objects bhool gaye** → Manually packets padh rahe ho
   - File download hui hai toh Export Objects use karo — bahut easy hai

5. **Sirf source IP dekha** → Destination bhi important hai
   - Attack ka source aur target dono note karo

---

## Quick Decision Tree

```
PCAP file mili — kya karu?
  │
  ├─ STEP 1: Overview lo
  │   ├─ Protocol hierarchy dekho (kya protocols hain?)
  │   └─ Top talkers dekho (kaun baat kar raha?)
  │
  ├─ STEP 2: Protocol ke hisaab se dhundo
  │   ├─ HTTP hai?
  │   │   ├─ http.authorization → Basic Auth (Base64 decode)
  │   │   ├─ POST requests → form data mein creds
  │   │   └─ Export Objects → downloaded files
  │   ├─ FTP hai? → USER + PASS commands (plaintext!)
  │   ├─ Telnet hai? → Follow TCP Stream (sab readable)
  │   ├─ DNS hai? → query names dhundo (suspicious domains)
  │   ├─ SMTP hai? → Follow stream → email body
  │   └─ TLS/SSL hai? → Certificate info / SNI
  │
  ├─ STEP 3: Attack detect karna hai?
  │   ├─ Bahut SYN packets (ek IP se) → Port Scan
  │   ├─ Abnormal packet count → DoS/Flood
  │   ├─ Same IP, different MACs → ARP Spoof
  │   └─ ICMP mein data → Data Exfiltration
  │
  └─ Kuch samajh nahi aa raha?
      └─ Follow TCP Stream #0 se shuru karo, ek ek stream padho
```
