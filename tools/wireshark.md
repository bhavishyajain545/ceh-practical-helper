# wireshark — the packet analyzer

> **The #1 tool for sniffing questions in CEH Practical.** Most "what password / what file / what user-agent / what DNS query" questions are solved in Wireshark with one good display filter. Memorize the filter table below.

**Install check (Parrot — already installed):** `wireshark --version`

**Open a pcap from CLI:** `wireshark capture.pcap &`

---

## 🎯 Cheat-flow: "What do I do with this pcap?"

| You need to find... | Do this | Why |
|---|---|---|
| **A password in HTTP POST** | Filter `http.request.method == "POST"` → right-click → Follow → HTTP Stream | Creds show in form body |
| **A file transferred over HTTP** | File → Export Objects → HTTP | Pulls out images, exes, HTML |
| **A file transferred over SMB** | File → Export Objects → SMB | Rips shared files |
| **Who talked to whom most** | Statistics → Conversations → TCP tab, sort by Bytes | Finds top talkers |
| **All DNS queries** | Filter `dns` | See lookups |
| **A specific host's traffic** | Filter `ip.addr == 10.10.10.5` | Both directions |
| **Only traffic on one port** | Filter `tcp.port == 21` | e.g. FTP |
| **The 3-way handshake / SYN scan** | Filter `tcp.flags.syn == 1 and tcp.flags.ack == 0` | SYN-only packets |
| **Telnet / FTP cleartext creds** | Filter `telnet` or `ftp` → Follow TCP Stream | Plaintext login |
| **A WPA handshake** | Filter `eapol` | Verify 4-way handshake captured |
| **Credentials across protocols** | Edit → Preferences → Protocols, or use `tshark -z credentials` | Auto-extract |

---

## 🔑 Display filters you MUST know cold

> **Display filters ≠ capture filters.** Display filter uses `==`, `and`, `or`, `!`. Capture filter uses BPF syntax (`host`, `port`). See [gotchas](#gotchas).

### IP / host
| Filter | What it matches |
|---|---|
| `ip.addr == 10.10.10.5` | Src OR dst is this IP |
| `ip.src == 10.10.10.5` | Only source |
| `ip.dst == 10.10.10.5` | Only destination |
| `ip.addr == 10.10.10.0/24` | Whole subnet |
| `!(ip.addr == 10.10.10.5)` | Exclude this host |

### TCP / UDP / ports
| Filter | What it matches |
|---|---|
| `tcp.port == 80` | TCP port 80 either direction |
| `tcp.srcport == 443` | Source port only |
| `tcp.dstport == 22` | Destination port only |
| `udp.port == 53` | UDP port 53 |
| `tcp.port == 80 or tcp.port == 443` | HTTP + HTTPS |
| `tcp.flags.syn == 1 and tcp.flags.ack == 0` | SYN only (scans) |
| `tcp.flags.reset == 1` | RSTs (closed ports) |
| `tcp.flags.fin == 1` | FIN packets |
| `tcp.analysis.retransmission` | Retransmits |

### Protocols
| Filter | What it matches |
|---|---|
| `http` | All HTTP |
| `http.request` | Only HTTP requests |
| `http.response` | Only HTTP responses |
| `http.request.method == "POST"` | **POST requests (creds!)** |
| `http.request.method == "GET"` | GET requests |
| `http.request.uri contains "login"` | URI substring |
| `http.host == "victim.com"` | Host header |
| `http.user_agent contains "sqlmap"` | UA filter |
| `http.response.code == 200` | HTTP status |
| `dns` | All DNS |
| `dns.qry.name contains "google"` | DNS query name |
| `dns.flags.response == 0` | DNS queries only |
| `ftp` | FTP control |
| `ftp.request.command == "USER"` | FTP logins |
| `telnet` | Telnet (cleartext) |
| `smtp` | Mail |
| `smb` or `smb2` | SMB |
| `arp` | ARP (spot poisoning) |
| `icmp` | Pings |
| `eapol` | **WPA handshake** |
| `tls.handshake.type == 1` | TLS Client Hello (see SNI) |

### Content / string search
| Filter | What it matches |
|---|---|
| `frame contains "password"` | Byte string anywhere |
| `tcp contains "admin"` | String in TCP payload |
| `http.authorization` | Basic-Auth header present |

---

## 🔑 Capture filters (BPF syntax — totally different)

Used at **Capture → Options → Capture filter**, not in the display bar.

| Filter | Meaning |
|---|---|
| `host 10.10.10.5` | To/from this host |
| `src host 10.10.10.5` | Source only |
| `dst host 10.10.10.5` | Destination only |
| `port 80` | Port 80 |
| `tcp port 443` | TCP 443 |
| `udp port 53` | UDP 53 |
| `net 10.10.10.0/24` | Subnet |
| `not arp` | Exclude ARP |
| `host 10.0.0.1 and port 22` | Combined |

---

## 🧪 Menu walkthroughs

### File menu basics
- **File → Open** — load a pcap
- **File → Export Specified Packets** — save filtered view as new pcap
- **File → Export Objects → HTTP** — dump every file transferred over HTTP
- **File → Export Objects → SMB** — same for SMB shares
- **File → Export Objects → FTP-DATA / TFTP / IMF** — other protocols

### Statistics menu (exam gold)
- **Statistics → Conversations** — per-endpoint byte counts; TCP tab shows top talkers
- **Statistics → Endpoints** — per-IP totals, includes GeoIP
- **Statistics → Protocol Hierarchy** — % of each protocol in capture
- **Statistics → I/O Graph** — spot spikes (DoS, exfil)
- **Statistics → HTTP → Requests** — every URL hit
- **Statistics → DNS** — query breakdown

### Follow stream (the shortcut you'll use constantly)
Right-click any packet → **Follow → TCP Stream / UDP Stream / HTTP Stream / TLS Stream**. Reassembles the full conversation in readable form — where cleartext passwords usually appear.

---

## 📋 Command recipes (copy-paste)

```bash
# Open a pcap
wireshark capture.pcap &

# Headless / terminal-only analysis with tshark
tshark -r capture.pcap -Y 'http.request.method == "POST"'
tshark -r capture.pcap -Y 'dns' -T fields -e dns.qry.name
tshark -r capture.pcap -z conv,tcp               # conversations
tshark -r capture.pcap -z credentials            # auto-extract creds
tshark -r capture.pcap -z http,tree              # HTTP stats
tshark -r capture.pcap --export-objects http,./out

# Live capture on wlan0 with capture filter
sudo tshark -i wlan0 -f "tcp port 80" -w web.pcap

# Verify a WPA 4-way handshake was captured
tshark -r wpa.pcap -Y eapol
```

---

## ⚠️ Gotchas

- **Display filter vs capture filter.** Display filter bar uses `tcp.port == 80`. Capture filter dialog uses `tcp port 80` (no dots, no `==`). Mixing them is the #1 newbie error.
- **Comparison operator is `==`**, not `=`. Wireshark will silently accept `=` in some versions and give wrong results.
- **`ip.addr != 10.0.0.1` is a trap.** Use `!(ip.addr == 10.0.0.1)` — the first form matches any packet where *either* endpoint is not that IP (so it matches everything).
- **String match is `contains`**, not `LIKE` or `~=`.
- **Export Objects for SMB** only works if the full SMB read was captured — partial streams won't reassemble.
- **Promiscuous mode** needs to be enabled on the interface to see traffic not destined for you.
- **Monitor mode** (wireless) is different from promiscuous — you need monitor mode + a supported adapter to see 802.11 mgmt frames. See [aircrack-ng](aircrack-ng.md).
- **WPA handshake verification:** after capturing with airodump, open the `.cap` in Wireshark and filter `eapol` — you need all 4 messages, otherwise aircrack will fail.
- **Case sensitivity:** protocol names are lowercase (`http`, not `HTTP`).

---

## 🔗 Related

- [tcpdump](tcpdump.md) — same captures, CLI
- [aircrack-ng](aircrack-ng.md) — verify WPA handshake in Wireshark with `eapol`
- [ettercap](ettercap.md) / [bettercap](bettercap.md) — generate the traffic you then sniff
- [08-sniffing domain README](../07-sniffing/README.md)
