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
| `-sA` | ACK scan | Map firewall rules |
| `-sN` `-sF` `-sX` | NULL / FIN / Xmas | Bypass simple firewalls |
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
- [Scanning question bank](../questions/02-scanning.md)
