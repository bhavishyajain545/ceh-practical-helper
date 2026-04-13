# tcpdump — CLI packet sniffer

> **Wireshark's little brother.** When you're on a shell with no GUI, or need to capture a pcap to later open in Wireshark, `tcpdump` is the answer. BPF filter syntax is identical to Wireshark capture filters.

**Install check (Parrot — already installed):** `tcpdump --version`

---

## 🎯 Cheat-flow: "What am I trying to do?"

| You need to... | Run this | Why |
|---|---|---|
| **List interfaces** | `tcpdump -D` | Find the right `-i` |
| Capture everything on an iface | `sudo tcpdump -i eth0` | Live dump to screen |
| **Save to pcap** for Wireshark later | `sudo tcpdump -i eth0 -w out.pcap` | Open in wireshark |
| **Read a pcap** from disk | `tcpdump -r out.pcap` | No capture needed |
| Capture only one host's traffic | `sudo tcpdump -i eth0 host 10.10.10.5` | BPF host filter |
| Capture only one port | `sudo tcpdump -i eth0 port 80` | HTTP traffic |
| See packet contents in ASCII | `sudo tcpdump -i eth0 -A port 80` | Show payload |
| See full hex + ASCII | `sudo tcpdump -i eth0 -XX port 80` | Forensics |
| Skip DNS lookups (faster) | `sudo tcpdump -n -i eth0` | No reverse DNS |
| Stop after N packets | `sudo tcpdump -c 100 -i eth0` | Limit output |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-i <iface>` | Interface to capture on (`-i any` for all) |
| `-D` | List available interfaces |
| `-w file.pcap` | **Write** raw packets to pcap file |
| `-r file.pcap` | **Read** packets from pcap file |
| `-n` | No DNS name resolution |
| `-nn` | No DNS *and* no port-name resolution |
| `-c <N>` | Stop after N packets |
| `-s 0` | Snaplen 0 = capture full packet (default on modern tcpdump) |
| `-v` / `-vv` / `-vvv` | Verbose |
| `-A` | Print packet in ASCII |
| `-X` | Print packet in hex + ASCII |
| `-XX` | Same, plus link-layer header |
| `-e` | Include Ethernet header (MAC addresses) |
| `-tttt` | Human-readable timestamps |
| `-q` | Quick/quiet output |
| `-Z root` | Don't drop privileges (rarely needed) |
| `-G <sec> -w file-%F-%H%M%S.pcap` | Rotate captures by time |

---

## 🔑 BPF filter primitives

Filters are positional arguments after the flags. Combine with `and` / `or` / `not` (or `&&` / `||` / `!`).

### Host / network
| Filter | Meaning |
|---|---|
| `host 10.10.10.5` | To or from this host |
| `src host 10.10.10.5` | Source only |
| `dst host 10.10.10.5` | Destination only |
| `net 10.10.10.0/24` | Subnet |
| `src net 10.10.10.0/24` | Source subnet |
| `ether host aa:bb:cc:dd:ee:ff` | MAC address |

### Port / protocol
| Filter | Meaning |
|---|---|
| `port 80` | Either direction |
| `src port 443` | Source port |
| `dst port 22` | Destination port |
| `portrange 1-1024` | Range |
| `tcp` / `udp` / `icmp` / `arp` | Protocol |
| `tcp port 80` | Combined |
| `udp port 53` | DNS |
| `icmp` | Pings |

### Combined
```
host 10.0.0.1 and port 22
tcp and not port 22
src 10.0.0.1 and dst port 80
(port 80 or port 443) and host 10.10.10.5
```

### TCP flags (scan detection)
| Filter | Meaning |
|---|---|
| `'tcp[tcpflags] & tcp-syn != 0'` | Any SYN |
| `'tcp[tcpflags] == tcp-syn'` | SYN-only (stealth scan) |
| `'tcp[tcpflags] & (tcp-syn\|tcp-fin) != 0'` | SYN or FIN |
| `'tcp[13] == 0x02'` | SYN (raw byte) |
| `'tcp[13] == 0x12'` | SYN-ACK |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. List capture interfaces
sudo tcpdump -D

# 2. Classic: save all traffic on eth0 to a pcap
sudo tcpdump -i eth0 -s 0 -w capture.pcap

# 3. Capture HTTP only
sudo tcpdump -i eth0 -s 0 -w http.pcap 'tcp port 80'

# 4. Capture traffic to/from one host
sudo tcpdump -i eth0 -nn host 10.10.10.5

# 5. Live view of HTTP payload in ASCII
sudo tcpdump -i eth0 -A -s 0 'tcp port 80'

# 6. Find cleartext passwords (quick & dirty)
sudo tcpdump -i eth0 -A -s 0 'tcp port 21 or tcp port 23 or tcp port 80' | grep -i 'pass\|user'

# 7. Read back a pcap, filter for a host
tcpdump -r capture.pcap -nn host 10.10.10.5

# 8. Detect a SYN scan
sudo tcpdump -i eth0 -nn 'tcp[tcpflags] == tcp-syn'

# 9. Rotate captures every 60s
sudo tcpdump -i eth0 -G 60 -w 'cap-%F-%H%M%S.pcap'

# 10. Capture then hand off to Wireshark
sudo tcpdump -i eth0 -w /tmp/out.pcap &
# ... run attack ...
sudo pkill tcpdump
wireshark /tmp/out.pcap &
```

---

## ⚠️ Gotchas

- **Needs root** (`sudo`) to capture. Reading a pcap (`-r`) does not.
- **BPF syntax, not Wireshark display-filter syntax.** `host 10.0.0.1`, NOT `ip.addr == 10.0.0.1`. See [wireshark](wireshark.md) for the display-filter equivalents.
- **Quote complex filters** in single quotes so shell doesn't eat `|` or `()`.
- **Snaplen:** old tcpdump defaulted to 68 bytes, truncating payload. Modern default is 0 (full). Force with `-s 0` if unsure.
- **DNS lookups slow output** — use `-n` or `-nn` every time.
- **`-i any`** on Linux gives a pseudo-interface — useful when you don't know which iface the traffic uses.
- **Permissions:** to let a non-root user capture, `sudo setcap cap_net_raw,cap_net_admin=eip $(which tcpdump)`.
- **Promiscuous mode** is on by default. Disable with `-p` if it changes link state.
- **Packet loss under high load** — use `-w` (write to disk) rather than screen printing; parsing is slower than I/O.

---

## 🔗 Related

- [wireshark](wireshark.md) — GUI analysis of the pcap you just captured
- [aircrack-ng](aircrack-ng.md) — airodump-ng is essentially tcpdump for 802.11
- [08-sniffing domain README](../08-sniffing/README.md)
