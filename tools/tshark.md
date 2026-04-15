# tshark — Wireshark CLI

> **Command-line Wireshark.** Captures + parses pcaps with display filters. Use when you want scriptable pcap analysis (grep for creds, extract specific fields, count conversations) without opening the GUI. CEH: "find the password in this pcap".

**Install check (Parrot):** `tshark -v` (install: `sudo apt install tshark`).

Non-root users need to be in `wireshark` group: `sudo usermod -aG wireshark $USER`; logout/login.

---

## 🎯 Cheat-flow

| Goal | Command |
|---|---|
| **Live capture** on interface | `sudo tshark -i eth0` |
| List interfaces | `tshark -D` |
| Capture N packets to file | `sudo tshark -i eth0 -c 100 -w cap.pcap` |
| **Read pcap** | `tshark -r cap.pcap` |
| **Display filter** (read) | `tshark -r cap.pcap -Y "http.request"` |
| Capture filter (write) | `sudo tshark -i eth0 -f "port 80" -w http.pcap` |
| Show only specific fields | `tshark -r cap.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport` |
| **Extract HTTP creds** | `tshark -r cap.pcap -Y "http.authorization" -T fields -e http.authorization` |
| **Extract FTP creds** | `tshark -r cap.pcap -Y "ftp.request.command == USER \|\| ftp.request.command == PASS" -T fields -e ftp.request.arg` |
| Follow TCP stream by index | `tshark -r cap.pcap -qz "follow,tcp,ascii,0"` |
| DNS queries only | `tshark -r cap.pcap -Y "dns" -T fields -e dns.qry.name` |
| HTTP URIs | `tshark -r cap.pcap -Y "http.request" -T fields -e http.host -e http.request.uri` |
| Count conversations | `tshark -r cap.pcap -qz conv,tcp` |
| IO statistics | `tshark -r cap.pcap -qz io,stat,1` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-i <iface>` | Interface to capture on |
| `-D` | List interfaces |
| `-r <file>` | Read pcap |
| `-w <file>` | Write pcap |
| `-c <n>` | Stop after n packets |
| `-f "<bpf>"` | **Capture** filter (BPF syntax) |
| `-Y "<expr>"` | **Display** filter (wireshark syntax) |
| `-T fields` | Output only specific fields |
| `-e <field>` | Field to extract (repeatable) |
| `-T json` | JSON output |
| `-T pdml` | XML output |
| `-q` | Quiet (for `-z` stats) |
| `-z <stat>` | Statistics (conv, io, follow, http,tree, …) |
| `-n` | No name resolution (faster) |
| `-2` | Two-pass analysis (needed for some filters) |
| `-V` | Verbose packet details |
| `-x` | Hex+ASCII dump |
| `-C <profile>` | Use wireshark profile |

---

## ⚡ CEH power patterns

### Find all HTTP POST passwords in a pcap
```bash
tshark -r cap.pcap -Y "http.request.method == POST" -T fields -e http.host -e http.file_data
```

### Extract Basic Auth header (base64-encoded creds)
```bash
tshark -r cap.pcap -Y "http.authorization contains Basic" -T fields -e http.authorization \
  | awk '{print $2}' | base64 -d
```

### Dump all FTP USER/PASS pairs
```bash
tshark -r cap.pcap -Y "ftp" -T fields -e ftp.request.command -e ftp.request.arg
```

### Carve an HTTP-downloaded file from pcap
```bash
tshark -r cap.pcap -q --export-objects http,./out/
ls out/
```

### Who talked the most?
```bash
tshark -r cap.pcap -qz conv,ip
```

---

## ⚠️ Gotchas

- **`-f` vs `-Y`:** `-f` is BPF (kernel-level, during capture), `-Y` is wireshark display filter (post-capture). Don't mix syntax.
- **Needs root** for live capture (or wireshark group membership).
- Very large pcaps → use `-2` for two-pass; or split with `editcap -c 10000 big.pcap split`.
- `-T fields` without `-e` prints nothing.
- BPF is far less expressive than display filters — for complex logic, capture broadly then filter with `-Y`.
- Some extraction (`--export-objects`) requires `-q` quiet mode.

---

## 🧠 tshark vs wireshark vs tcpdump

| Use | Tool |
|---|---|
| GUI exploration, visual follow-stream | **wireshark** |
| Scripted pcap parsing, field extraction | **tshark** |
| Simple live capture on a remote box | **tcpdump** (tiny, always available) |
| Carve files from HTTP/SMB | **tshark --export-objects** or **NetworkMiner** |

---

## 🔎 Display-filter parity with wireshark.md

Every display filter documented in [wireshark.md](wireshark.md) works verbatim under `tshark -Y "<filter>"`. High-value ones to memorize for CLI use:

### Ethernet / MAC
```bash
tshark -r cap.pcap -Y 'eth.addr == aa:bb:cc:dd:ee:ff'
tshark -r cap.pcap -Y 'eth.dst == ff:ff:ff:ff:ff:ff'     # broadcasts
```

### ICMP
```bash
tshark -r cap.pcap -Y 'icmp.type == 8'                    # ping requests
tshark -r cap.pcap -Y 'icmp.type == 3'                    # dest unreachable
tshark -r cap.pcap -Y 'icmp.type == 11'                   # traceroute
```

### HTTP / TLS / DNS / SMB / FTP
```bash
tshark -r cap.pcap -Y 'http.response.code == 200'
tshark -r cap.pcap -Y 'http.request.method == "POST"'
tshark -r cap.pcap -Y 'tls.handshake.type == 1' -T fields -e tls.handshake.extensions_server_name
tshark -r cap.pcap -Y 'dns.qry.name contains "evil"'
tshark -r cap.pcap -Y 'smb2'
tshark -r cap.pcap -Y 'ftp-data'                          # file transfer bytes
```

### IP / TCP / ports
```bash
tshark -r cap.pcap -Y 'ip.addr == 10.10.10.5'
tshark -r cap.pcap -Y 'tcp.flags.syn == 1 and tcp.flags.ack == 0'   # SYN scan
tshark -r cap.pcap -Y 'tcp.analysis.retransmission'
```

### Export objects via CLI (same as Wireshark's File → Export Objects)
```bash
tshark -r cap.pcap -q --export-objects http,./http_out
tshark -r cap.pcap -q --export-objects smb,./smb_out
tshark -r cap.pcap -q --export-objects tftp,./tftp_out
tshark -r cap.pcap -q --export-objects imf,./email_out    # email (IMF)
```

For the full list, treat `wireshark.md` as the authoritative display-filter reference — anything with `.` syntax works here.

---

## 📖 See also
- [wireshark.md](wireshark.md) — the GUI and the authoritative filter list
- [tcpdump.md](tcpdump.md)
- [ettercap.md](ettercap.md)
