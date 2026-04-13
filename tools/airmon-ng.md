# airmon-ng — enable wireless monitor mode

> **Step 0 of every wifi attack.** Puts your card into monitor mode so airodump-ng / aireplay-ng can see raw 802.11 frames.

**Install check:** `airmon-ng --help`

---

## 🎯 Cheat-flow

```bash
sudo airmon-ng                        # list wireless interfaces + drivers
sudo airmon-ng check                  # show interfering processes
sudo airmon-ng check kill             # kill NetworkManager / wpa_supplicant
sudo airmon-ng start wlan0            # enable monitor mode → creates wlan0mon
sudo airmon-ng stop wlan0mon          # back to managed mode
```

---

## 🔑 Commands

| Command | Purpose |
|---|---|
| `airmon-ng` | List interfaces, chipset, driver |
| `airmon-ng start <iface> [channel]` | Start monitor mode (optional channel) |
| `airmon-ng stop <iface>` | Stop monitor mode |
| `airmon-ng check` | List processes that can interfere |
| `airmon-ng check kill` | Kill all interfering processes |

---

## 📋 Recipes

```bash
# 1. Standard "get ready to attack" sequence
sudo airmon-ng check kill
sudo airmon-ng start wlan0
iwconfig                    # confirm: wlan0mon   Mode:Monitor

# 2. Manual alternative using iw (when airmon-ng misbehaves)
sudo ip link set wlan0 down
sudo iw dev wlan0 set type monitor
sudo ip link set wlan0 up

# 3. Restore managed mode + networking
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager
```

---

## ⚠️ Gotchas

- Interface rename: older distros → `wlan0mon`, some → `mon0`, some keep `wlan0`. Run `iwconfig` to see the real name.
- **NetworkManager/wpa_supplicant will fight you** — always run `airmon-ng check kill` first.
- Not every chipset supports monitor mode. Common good chipsets: Atheros AR9271, Ralink RT3070/RT5370, Realtek RTL8812AU.
- After stopping monitor mode, Wi-Fi may be down — restart NetworkManager.
- Card must also support **packet injection** — test with `aireplay-ng -9 wlan0mon`.

---

## 🔗 Related

- [airodump-ng](airodump-ng.md) · [aireplay-ng](aireplay-ng.md) · [aircrack-ng](aircrack-ng.md) · [wifite](wifite.md) · [reaver](reaver.md)
