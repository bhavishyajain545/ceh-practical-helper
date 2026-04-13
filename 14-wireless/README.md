# 14 — Wireless

> Wireless questions in CEH Practical are usually offline: you get a `.cap` or `.pcap` and a wordlist, and you crack it. Sometimes you run the full aircrack-ng flow against a simulated AP.

## 🧭 Decision tree — "I see a wireless question"

```
What encryption?
│
├── WEP
│   ├── airodump-ng → capture IVs
│   ├── aireplay-ng → ARP replay to speed IVs
│   └── aircrack-ng capture.cap   → key recovered at ~20-40k IVs
│
├── WPA / WPA2 (PSK)
│   ├── airodump-ng → capture on channel
│   ├── aireplay-ng --deauth → force handshake
│   ├── Wait for "WPA handshake: <BSSID>" in airodump top bar
│   └── aircrack-ng -w <wordlist> -b <BSSID> capture.cap
│
├── WPA3
│   └── Offline dict crack not feasible — look for downgrade / Dragonblood
│
├── WPS enabled?
│   ├── wash -i <iface>               → find WPS APs
│   └── reaver -i <iface> -b <BSSID>  → PIN brute / Pixie Dust
│
└── "Just crack this .cap they gave me"
    └── aircrack-ng -w /usr/share/wordlists/rockyou.txt capture.cap
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — aircrack-ng + wifite + reaver recipes
- **[walkthroughs.md](walkthroughs.md)** — full capture→crack flows
- Question bank: **[../questions/by-domain/14-wireless.md](../questions/by-domain/14-wireless.md)**

## 🛠 Tools used in this domain

- **[aircrack-ng](../tools/aircrack-ng.md)** — the suite (airmon, airodump, aireplay, aircrack)
- **[wifite](../tools/wifite.md)** — automates the whole dance
- **[reaver](../tools/reaver.md)** — WPS PIN attack
- **[hashcat](../tools/hashcat.md)** — GPU cracking for WPA (mode 22000)

## ✅ Domain checklist

- [ ] Put a card into monitor mode: `airmon-ng start wlan0`
- [ ] Capture on a channel: `airodump-ng -c 6 --bssid ... -w cap wlan0mon`
- [ ] Deauth a client: `aireplay-ng --deauth 5 -a <BSSID> wlan0mon`
- [ ] Recognize a WPA handshake line in airodump
- [ ] Crack a WPA handshake with rockyou
- [ ] Run reaver / wash against WPS
- [ ] Done all questions in [the question bank](../questions/by-domain/14-wireless.md)
