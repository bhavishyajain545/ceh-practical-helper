# 07 — Sniffing

> Packet capture and analysis. Expect 2–3 questions that hand you a `.pcap` and ask "what's the password / what's the URL / what file was downloaded", plus possibly a live ARP spoofing task.

## 🧭 Decision tree — "I see a sniffing question"

```
What are you given?
│
├── A .pcap file
│   └── Wireshark → File → Open → use display filters (see below)
│       Quick CLI triage: tcpdump -r file.pcap -nn
│
├── "Intercept traffic between host A and the gateway on the LAN"
│   ├── Ettercap GUI  → Hosts → Scan → Targets → ARP poisoning
│   └── Bettercap CLI → net.probe on; set arp.spoof.targets <A>; arp.spoof on
│
├── "ARP poison only"
│   └── arpspoof -i eth0 -t <victim> <gateway>
│       (and reverse: arpspoof -i eth0 -t <gateway> <victim>)
│
├── "DNS spoof to redirect victim"
│   └── dnsspoof -i eth0 -f hosts.txt       (after arpspoof is running)
│       Or bettercap: set dns.spoof.domains *.example.com; dns.spoof on
│
└── "Capture creds from HTTP/FTP/Telnet traffic"
    └── Run sniffer, filter for the protocol, look at POST body / USER+PASS
```

## 📄 Wireshark filter cheat sheet (memorize these)

| Goal | Display filter |
|---|---|
| Only HTTP | `http` |
| HTTP POST (logins) | `http.request.method == "POST"` |
| HTTP to / from host | `ip.addr == 10.10.10.5 and http` |
| DNS queries | `dns` |
| FTP (cleartext creds) | `ftp` |
| Telnet | `telnet` |
| SMB | `smb or smb2` |
| Kerberos | `kerberos` |
| TCP stream N | `tcp.stream eq 5` |
| By port | `tcp.port == 4444` |
| Contains string | `frame contains "password"` |
| Only one conversation | right-click packet → Follow → TCP Stream |
| ARP (spot spoofing) | `arp` |
| ICMP | `icmp` |

**Capture filter** (different syntax — BPF — set before capture):
`host 10.10.10.5`, `port 80`, `not broadcast`, `tcp port 80 or tcp port 443`

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/07-sniffing.md](../questions/07-sniffing.md)**

## 🛠 Tools used in this domain

- **[wireshark](../tools/wireshark.md)** ← the big one
- **[tcpdump](../tools/tcpdump.md)** — CLI sniffer
- **[ettercap](../tools/ettercap.md)** — MITM framework (GUI + CLI)
- **[bettercap](../tools/bettercap.md)** — modern MITM
- **[arpspoof](../tools/arpspoof.md)** — simple ARP poisoner (dsniff suite)
- **[dnsspoof](../tools/dnsspoof.md)** — DNS response forging

## ✅ Domain checklist

- [ ] Open a pcap in Wireshark and extract HTTP POST credentials
- [ ] Follow a TCP stream and reconstruct a chat / file transfer
- [ ] Write display filters for HTTP, DNS, FTP, SMB, Kerberos from memory
- [ ] Enable IP forwarding and ARP-spoof a target with arpspoof
- [ ] Run a full bettercap MITM (arp.spoof + net.sniff + http.proxy)
- [ ] Recognize ARP spoofing in a pcap (duplicate MAC for same IP)
- [ ] Done all questions in [the question bank](../questions/07-sniffing.md)
