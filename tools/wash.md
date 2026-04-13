# wash — find WPS-enabled APs

> **reaver's scanner sibling.** Lists nearby APs with WPS, their lock status, and version.

**Install check:** `wash --help` (bundled with reaver)

---

## 🎯 Cheat-flow

```bash
sudo airmon-ng start wlan0
sudo wash -i wlan0mon                      # scan all channels
sudo wash -i wlan0mon -c 6                 # fix channel
sudo wash -i wlan0mon -5                   # include 5 GHz (if card supports)
sudo wash -i wlan0mon -j                   # JSON output
```

---

## 🔑 Columns in output

```
BSSID              Ch  dBm  WPS  Lck  Vendor     ESSID
AA:BB:CC:DD:EE:FF  6  -45   2.0  No   Broadcom   TargetNet
```

| Column | Meaning |
|---|---|
| `WPS` | WPS version (1.0 / 2.0) |
| **`Lck`** | `No` = attackable, `Yes` = locked out (wait) |
| `Vendor` | Chipset — hints at Pixie Dust vulnerability |

**Targets:** WPS version 1.0, `Lck=No`, Broadcom/Ralink/Realtek chipset → try Pixie Dust with [reaver](reaver.md) `-K 1`.

---

## ⚠️ Gotchas

- Monitor mode required (`airmon-ng start wlan0`).
- Not every router advertises WPS — absence is not proof.
- After failed reaver runs the AP may flip to `Lck=Yes` temporarily.

---

## 🔗 Related

- [reaver](reaver.md) · [wifite](wifite.md) · [airodump-ng](airodump-ng.md) · [airmon-ng](airmon-ng.md)
