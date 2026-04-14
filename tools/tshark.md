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

## 📖 See also
- [wireshark.md](wireshark.md)
- [tcpdump.md](tcpdump.md)
- [ettercap.md](ettercap.md)
