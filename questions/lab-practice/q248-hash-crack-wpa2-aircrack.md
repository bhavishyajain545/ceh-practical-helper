# Q248 — Crack WPA2 Handshake

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🔴 Hard |
| **Tools** | `aircrack-ng` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"A WPA2 handshake has been captured in `handshake.cap`. Crack it using aircrack-ng with rockyou.txt. Report the WiFi password."

---

## 🎯 Flag Format

```
ssid=<network_name>; password=<wifi_password>
```

---

## ✅ Solution

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <BSSID> handshake.cap
```

**Answer:** `ssid=CEH-Lab-WiFi; password=sunshine1`

---

## 🤖 Claude Setup Prompt

Provide a pre-captured WPA2 handshake pcap file with a rockyou password.

Report back: "Lab ready for Q248 — handshake.cap with WPA2 handshake ready"
