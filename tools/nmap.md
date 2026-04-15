# nmap — the network mapper

> **The single most-used tool in CEH Practical.** ~30% of questions involve nmap somewhere. Master this page and you've covered a lot of ground.

**Install check (Parrot — already installed):** `nmap --version`

---

## 🎯 Cheat-flow: "What scan do I run?"

| You need to find... | Run this | Why |
|---|---|---|
| Which hosts are alive on a /24 | `nmap -sn 10.10.10.0/24` | Ping sweep, no port scan |
| **All open ports** on one host | `nmap -p- -T4 <IP>` | All 65535 ports |
| **Service versions** (most exam Qs) | `nmap -sV -sC -p- <IP>` | Version + default scripts |
| OS detection | `nmap -O <IP>` | Needs root |
| Stealth scan (firewall present) | `nmap -sS -Pn <IP>` | SYN scan, skip ping |
| UDP services (SNMP, DNS, TFTP) | `nmap -sU -p 53,69,161 <IP>` | UDP is slow — limit ports |
| **Run a specific NSE script** | `nmap --script <name> -p <port> <IP>` | See [NSE section](#nse-scripts-the-money-section) |
| Scan a list of IPs | `nmap -iL targets.txt` | One IP per line |
| Save output (always do this) | `nmap -oN scan.txt -oX scan.xml ...` | Normal + XML |

---

## 🔑 Flags you must know cold

### Host discovery
| Flag | Meaning |
|---|---|
| `-sn` | Ping sweep only, no port scan |
| `-Pn` | Skip ping, treat host as alive (use when host blocks ICMP) |
| `-PS<ports>` | TCP SYN ping on specific ports |
| `-PU<ports>` | UDP ping |
| `-n` | No DNS resolution (faster) |

### Port specification
| Flag | Meaning |
|---|---|
| `-p 80` | Single port |
| `-p 1-1000` | Range |
| `-p-` | **All 65535 ports** |
| `-p T:80,U:53` | Mixed TCP + UDP |
| `--top-ports 100` | Top 100 most common |
| `-F` | Fast — top 100 ports |

### Scan techniques
| Flag | Meaning | When to use |
|---|---|---|
| `-sS` | SYN stealth scan (default if root) | Default fast scan |
| `-sT` | Full TCP connect | If you're not root |
| `-sU` | UDP scan | SNMP, DNS, TFTP, NTP |
| `-sA` | **ACK scan** | Map firewall rules (distinguish stateful vs stateless) |
| `-sW` | **Window scan** | Like ACK but reads TCP window size to infer open/closed |
| `-sM` | **Maimon scan** (FIN/ACK) | Evasion against some BSD stacks |
| `-sN` `-sF` `-sX` | NULL / FIN / Xmas | Bypass simple firewalls |
| `-sY` | **SCTP INIT** | SCTP port discovery (CEH blueprint, telecom stacks) |
| `-sZ` | **SCTP COOKIE-ECHO** | Stealthier SCTP probe |
| `-sO` | **IP protocol scan** | Discover which IP protocols (ICMP/TCP/UDP/GRE/ESP...) respond |
| `-sV` | **Version detection** | Almost every exam Q |
| `-O` | OS detection | Needs root |
| `-A` | Aggressive — `-sV -O -sC --traceroute` | When you have time |

### Timing
| Flag | Speed | Use |
|---|---|---|
| `-T0` / `-T1` | Paranoid / sneaky | IDS evasion only |
| `-T3` | Default | Normal |
| `-T4` | **Aggressive** | **Default for exam** |
| `-T5` | Insane | Risk of dropped packets |

### Target selection extras
| Flag | Meaning |
|---|---|
| `-iR <N>` | Scan **N random** internet hosts (research / CEH "pick random targets") |
| `--exclude <host[,host]>` | Exclude specific hosts |
| `--excludefile <file>` | Exclude a list |
| `-e <iface>` | **Force source interface** (multi-NIC labs) |
| `--reason` | **Why** a port is in the state it's in (e.g. "syn-ack", "no-response") |
| `--script-args <k=v[,k=v]>` | Pass args to NSE scripts (e.g. `http-methods.url-path=/admin`) |
| `--max-rate <pps>` | Cap packets-per-second (rate limit the attack) |
| `--min-rate <pps>` | Force minimum PPS (speed up) |
| `--open` | Show only open ports (skip closed/filtered) |

### Output (ALWAYS save scans)
| Flag | Format |
|---|---|
| `-oN file.txt` | Normal (human) |
| `-oX file.xml` | XML (parse later) |
| `-oG file.gnmap` | Greppable |
| `-oA basename` | All three at once |

---

## 🧪 NSE Scripts (the money section)

NSE scripts answer ~half the "find X version / vulnerability / config" questions. Scripts live in `/usr/share/nmap/scripts/`.

**Run a script:**
```bash
nmap --script <script-name> -p <port> <IP>
```

**Run a category:**
```bash
nmap --script vuln <IP>             # all vuln-check scripts
nmap --script "smb-*" -p 445 <IP>   # all smb scripts
nmap -sC <IP>                       # default scripts (safe)
```

### High-value scripts by service

| Service | Port | Script | What it gives you |
|---|---|---|---|
| **SMB** | 445 | `smb-os-discovery` | Windows version, hostname, domain |
| SMB | 445 | `smb-enum-shares` | List shares |
| SMB | 445 | `smb-enum-users` | List users |
| **SMB vuln** | 445 | `smb-vuln-ms17-010` | EternalBlue check |
| SMB vuln | 445 | `smb-vuln-ms08-067` | older MS08-067 |
| **HTTP** | 80/443 | `http-title` | Page title |
| HTTP | 80 | `http-enum` | Common dirs |
| HTTP | 80 | `http-methods` | Allowed methods (PUT, DELETE) |
| HTTP | 80 | `http-headers` | Server headers |
| **FTP** | 21 | `ftp-anon` | Anonymous login allowed? |
| FTP | 21 | `ftp-vsftpd-backdoor` | vsftpd 2.3.4 backdoor |
| **SSH** | 22 | `ssh2-enum-algos` | Algorithms |
| SSH | 22 | `ssh-hostkey` | Host key |
| **DNS** | 53 | `dns-zone-transfer` | AXFR (huge if it works) |
| **SNMP** | 161 | `snmp-info` `snmp-brute` | Community strings |
| **SMTP** | 25 | `smtp-enum-users` | User enumeration |
| **MySQL** | 3306 | `mysql-empty-password` | Blank root pwd |
| MySQL | 3306 | `mysql-info` | Version |
| **RDP** | 3389 | `rdp-enum-encryption` | Encryption level |
| RDP | 3389 | `rdp-vuln-ms12-020` | Old RDP DoS bug |
| **SSL** | 443 | `ssl-cert` | Cert details (CN, dates) |
| SSL | 443 | `ssl-enum-ciphers` | Weak ciphers |
| SSL | 443 | `ssl-heartbleed` | Heartbleed |

### More NSE scripts worth knowing by name

| Script | What it does |
|---|---|
| `smb-os-discovery` | Windows version, hostname, domain (pair with `-p 445`) |
| `http-enum` | Dir / app fingerprint brute |
| `http-methods` | With `--script-args http-methods.url-path=/admin` (see Q017) |
| `ipv6-node-info` | IPv6 node info query (CEH IPv6 lab) |
| `vuln` category | Run every safe-ish vuln check: `nmap --script vuln <IP>` |
| `default` / `safe` / `auth` / `brute` / `discovery` / `dos` / `exploit` / `external` / `fuzzer` / `intrusive` / `malware` / `version` | Category names — use with `--script <category>` |

### Using `--script-args`

```bash
# Probe /admin with http-methods
nmap -p 80 --script http-methods --script-args http-methods.url-path=/admin <IP>

# snmp-brute with a custom community list
nmap -sU -p 161 --script snmp-brute --script-args snmp-brute.communitiesdb=communities.txt <IP>

# http-brute against a login form
nmap -p 80 --script http-brute --script-args \
  http-brute.path=/login.php,userdb=users.txt,passdb=pass.txt <IP>
```

### Find a script by keyword
```bash
ls /usr/share/nmap/scripts/ | grep <keyword>
# or
nmap --script-help "*smb*"
```

---

## 📋 Command recipes (copy-paste)

```bash
# 1. The "always run first" scan (background while you read the question)
nmap -sV -sC -p- -T4 -oA full <IP>

# 2. Quick top-1000 with version
nmap -sV --top-ports 1000 -oN quick.txt <IP>

# 3. UDP top 20 (slow but needed for SNMP / DNS / TFTP)
sudo nmap -sU --top-ports 20 -oN udp.txt <IP>

# 4. Vuln scan
nmap --script vuln -p- -oN vuln.txt <IP>

# 5. SMB everything
nmap -p 445 --script "smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-*" <IP>

# 6. HTTP everything
nmap -p 80,443,8080,8443 --script "http-title,http-headers,http-methods,http-enum" <IP>

# 7. Find live hosts on a subnet
nmap -sn 10.10.10.0/24 -oN live.txt

# 8. Detect a specific service version (most common Q type)
nmap -sV -p <PORT> <IP>
```

---

## 🌐 IPv6 Scanning (`-6`)

IPv6 targets are common in CEH scenario-based Qs. The `-6` flag switches nmap into IPv6 mode — most other flags work unchanged.

### Basic IPv6 scans

```bash
nmap -6 2001:db8::1                          # single IPv6 host
nmap -6 -sV 2001:db8::1                      # service versions
sudo nmap -6 -sS fe80::a00:27ff:fe12:3456%eth0   # link-local (need zone id)
nmap -6 --top-ports 100 2001:db8::/124       # small subnet
```

### Link-local quirks (VMware lab / local segment)
Link-local addresses (`fe80::/10`) **require a zone identifier** `%<iface>` so the kernel knows which NIC.

```bash
# Discover neighbors on eth0
ping6 -c 2 ff02::1%eth0                      # all-nodes multicast
ip -6 neigh show dev eth0                    # ARP-equivalent cache

# Scan discovered neighbor
sudo nmap -6 -sS fe80::20c:29ff:fe12:3456%eth0
```

### IPv6-specific NSE scripts
```bash
nmap -6 --script=ipv6-* <target>             # all IPv6 scripts
nmap -6 --script=targets-ipv6-multicast-*    # neighbor discovery via multicast
nmap -6 --script=dns-ip6-arpa-scan <prefix>  # reverse DNS sweep of /64
```

### Gotchas
- **No ping sweep on /64** — IPv6 subnets are too huge to sweep. Use neighbor discovery (`ip -6 neigh`, multicast ping `ff02::1`), DNS, or target lists.
- **VMware NAT doesn't route IPv6** between guests by default — only link-local works on the shared L2. Host-Only mode or a manual config change is needed for global IPv6 testing.
- **`-sn` (ping sweep)** with `-6` does ICMPv6 echo + NS — but still requires unicast targets, not a subnet scan.
- Zone identifier must match the interface with the source IPv6 address — wrong `%iface` → "no route to host".
- Windows Firewall blocks ICMPv6 by default → add `-Pn` if target looks down but is actually reachable via L2.

### CEH IPv6 keyword → command

| Question says | Command |
|---|---|
| "scan IPv6 target" | `nmap -6 <addr>` |
| "discover neighbors on the IPv6 segment" | `ping6 ff02::1%eth0` then `ip -6 neigh` |
| "scan link-local host" | `nmap -6 fe80::X%eth0` (zone id mandatory) |
| "run IPv6 NSE scripts" | `nmap -6 --script=ipv6-*` |

---

## 🥷 Firewall / IDS Evasion

CEH **loves** evasion questions. Know these cold — they're often worded as *"perform a decoy scan"*, *"fragment packets"*, *"scan using spoofed source port"*, *"idle/zombie scan"*.

### Decoy scan (`-D`)
Mask your real IP with fake attackers. IDS sees ~N sources, can't tell which is real.

```bash
sudo nmap -D RND:10 -sS 192.168.52.130              # 10 random decoys
sudo nmap -D 10.0.0.1,10.0.0.2,ME,10.0.0.3 <IP>     # manual list, ME = your real IP position
```

- `RND:<n>` → n random decoys
- **Must use `sudo`** (raw packets)
- **Decoys must be alive** or remote endpoints might flag traffic as spoofed — pick real-looking IPs in the target's neighborhood.

### Fragmentation (`-f`, `--mtu`)
Split each probe into tiny IP fragments — primitive IDS may not reassemble.

```bash
sudo nmap -f <IP>                  # 8-byte fragments
sudo nmap -ff <IP>                 # 16-byte fragments
sudo nmap --mtu 24 <IP>            # custom MTU (multiple of 8)
```

### Source port spoofing (`-g` / `--source-port`)
Many firewalls trust traffic from port 53 (DNS), 67 (DHCP), 88 (Kerberos), or 20 (FTP-DATA).

```bash
sudo nmap -g 53 -sS <IP>
sudo nmap --source-port 88 <IP>
```

### Idle / zombie scan (`-sI`)
Scan **entirely through a 3rd-party "zombie"** host with incremental IPID. Target never sees your IP.

```bash
# 1. Find a zombie with predictable IPID
sudo nmap -O -v <ZOMBIE_IP> | grep -i "IP ID Sequence"   # want "Incremental"

# 2. Scan target via zombie
sudo nmap -sI <ZOMBIE_IP> <TARGET_IP>
sudo nmap -sI <ZOMBIE_IP>:<port> <TARGET_IP>             # specify zombie's source port
```

- Zombie must be **idle** (no background traffic) and have **incremental IPID** (rare on modern OSes).
- Use `nmap --script ipidseq.nse` on a subnet to find candidates.

### MAC spoof (`--spoof-mac`)
Change source MAC on the wire (useful on same L2 segment).

```bash
sudo nmap --spoof-mac 0 <IP>                  # random MAC
sudo nmap --spoof-mac DE:AD:BE:EF:00:01 <IP>  # specific MAC
sudo nmap --spoof-mac Apple <IP>              # random MAC from Apple OUI
sudo nmap --spoof-mac Cisco <IP>              # OUI match
```

### Timing-based evasion (`-T0` / `--scan-delay`)
Slow scans to dodge rate-based IDS rules.

```bash
sudo nmap -T0 <IP>                      # paranoid (5min between probes)
sudo nmap -T1 <IP>                      # sneaky (15sec)
sudo nmap --scan-delay 5s <IP>
sudo nmap --max-scan-delay 10s --max-retries 1 <IP>
```

### Packet manipulation
| Flag | Effect |
|---|---|
| `--data-length <n>` | Append n random bytes to every probe (evades signature matching) |
| `--badsum` | Bad TCP/UDP checksum — replies mean a **bad firewall** (kernels drop bad checksums) |
| `--ip-options <opts>` | Set custom IP options (loose source routing, etc.) |
| `--ttl <n>` | Fixed TTL |
| `--randomize-hosts` | Shuffle target order when scanning multiple hosts |

### Combined evasion example (full stealth)
```bash
sudo nmap -sS -T2 -f --mtu 24 -D RND:5 -g 53 --data-length 25 --randomize-hosts -Pn <IP>
```

**Mental model:** Decoy confuses **source**, fragmentation confuses **signature matching**, source-port bypasses **rule**, idle hides **identity**, timing dodges **rate alarms**.

### Evasion cheat-table (CEH keyword → flag)

| Question says | Flag(s) |
|---|---|
| "decoy scan" / "cloaked source" | `-D RND:10` |
| "fragment packets" / "split probes" | `-f` or `--mtu 24` |
| "spoof source port" / "bypass firewall via DNS" | `-g 53` |
| "idle scan" / "zombie scan" / "scan through proxy host" | `-sI <zombie>` |
| "spoof MAC address" | `--spoof-mac 0` |
| "slow/stealth scan" | `-T0` or `-T1` |
| "append random data" / "evade signature" | `--data-length 25` |
| "check firewall filtering" via bad checksum | `--badsum` |

---

## ⚠️ Gotchas

- **Question asks "version"** → use `-sV`. Without it you only get port state.
- **Host shows "filtered" or no response** → add `-Pn` (skip ping).
- **UDP scans take forever** → always limit with `-p` or `--top-ports`.
- **Scripts need root** for some scan types (`-sS`, `-O`, `-sU`). Use `sudo`.
- **Answer format matters.** If question asks for the version, copy it *exactly* as nmap printed it (e.g. `vsftpd 2.3.4`, not `vsftpd2.3.4`).
- **NSE script not found?** Update db: `sudo nmap --script-updatedb`.
- **Scan too slow?** `-T4 -n --min-rate 1000`.
- **Output to file always** — you may need to refer back later in the exam.

---

## 🔗 Related

- [02-scanning domain README](../02-scanning/README.md)
- [Scanning playbook](../playbooks/scanning-playbook.md)
- [Scanning question bank](../questions/by-domain/02-scanning.md)
