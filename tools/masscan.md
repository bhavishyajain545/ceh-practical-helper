# masscan — Internet-scale port scanner

> **World's fastest port scanner.** Async TCP SYN scanning at millions of packets per second. Use it when nmap is too slow (large subnets, full 65k port sweep). CEH practical: "fast scan of /24" or "find port X across the lab subnet".

**Install check (Parrot):** `masscan --version` (install: `sudo apt install masscan`)

**Needs root** (raw sockets).

---

## 🎯 Cheat-flow: "Quick port discovery at scale"

| Goal | Command | Why |
|---|---|---|
| **All 65k ports, single host** | `sudo masscan -p1-65535 192.168.52.129 --rate 1000` | Full TCP sweep |
| Top common ports, subnet | `sudo masscan -p21,22,23,25,80,139,443,445,3306,3389 192.168.52.0/24 --rate 5000` | Fast recon |
| **Find SMB on /24** | `sudo masscan -p445 192.168.52.0/24 --rate 10000` | Lateral scope |
| UDP scan | `sudo masscan -pU:53,161 192.168.52.129 --rate 500` | DNS/SNMP |
| Save results | `sudo masscan -p- 192.168.52.129 --rate 1000 -oG masscan.gnmap` | Greppable out |
| Banner grab (limited) | `sudo masscan -p80,443 --banners 192.168.52.0/24 --rate 1000` | Needs `--source-ip` trick |
| Resume interrupted scan | `sudo masscan --resume paused.conf` | Continue |

**Typical CEH flow:** masscan for fast port discovery → feed open ports into nmap `-sV -sC` for version/script detail.

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-p<ports>` | Port list (e.g. `-p80,443,1-1000`) |
| `-pU:<ports>` | UDP ports |
| `--rate <pps>` | Packets per second (default 100; crank up carefully) |
| `--banners` | Grab banners after port open (needs conntrack tweak) |
| `--source-ip <IP>` | Spoof/alternate source IP |
| `--source-port <P>` | Fixed source port (evasion) |
| `--excludefile <f>` | Skip IPs in file |
| `-oG <file>` | Greppable output (nmap-compatible) |
| `-oX <file>` | XML output |
| `-oJ <file>` | JSON output |
| `--resume <conf>` | Resume from pause state |
| `--ping` | ICMP sweep |
| `-e <iface>` | Pick NIC |
| `--router-mac <MAC>` | Override gateway MAC |

---

## ⚡ Common patterns

### Full /24 sweep, top 100 ports
```bash
sudo masscan -p0-1000 192.168.52.0/24 --rate 5000 -oG subnet.gnmap
```

### Find every host with 445 open (SMB recon)
```bash
sudo masscan -p445 192.168.52.0/24 --rate 10000
```

### Feed masscan output into nmap
```bash
sudo masscan -p- 192.168.52.129 --rate 1000 -oG ms.gnmap
PORTS=$(grep -oP '\d+/open' ms.gnmap | cut -d/ -f1 | paste -sd,)
sudo nmap -sV -sC -p$PORTS 192.168.52.129
```

### Banner grab (requires iptables tweak so kernel doesn't RST)
```bash
sudo iptables -A OUTPUT -p tcp --tcp-flags RST RST -j DROP
sudo masscan -p80,443 --banners 192.168.52.129 --rate 1000
```

---

## ⚠️ Gotchas

- **Rate default is 100 pps** — slow. Increase with `--rate 1000` minimum for labs.
- **Kernel sends RSTs** — masscan uses its own TCP stack; kernel sees unexpected SYN-ACK and replies RST, breaking banner grab. Fix with iptables DROP rule above.
- **Hits conntrack limits** on the attacker box at high rates — drop rate or add iptables NOTRACK.
- **NOT stealthy** — generates huge packet volume; easy to detect.
- **Not as accurate as nmap** — prefer nmap for precise service detection. masscan = discovery, nmap = enumeration.
- **`--rate` is per-second budget** — not per-host. For /24 at 5000 pps, each host gets ~20 pps.
- Output format with `-oG` is nmap-compatible → easy to pipe into other tools.

---

## 🧠 When to use masscan vs nmap

| Scenario | Tool |
|---|---|
| Single host, detailed services | **nmap** (`-sV -sC`) |
| /16 or /24 quick port discovery | **masscan** |
| Need NSE scripts / version detection | **nmap** |
| Need to finish in under 30 sec | **masscan** |
| Need evasion (decoys, fragmentation) | **nmap** |
| CEH exam time crunch, scan subnet | **masscan** → **nmap** on findings |

---

## 📖 See also
- [nmap.md](nmap.md) — precise service/version detection after masscan
- [hping3.md](hping3.md) — single-packet crafting
- [playbooks/scanning-playbook.md](../playbooks/scanning-playbook.md)
