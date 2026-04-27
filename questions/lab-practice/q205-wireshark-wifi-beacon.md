# Q205 — Analyze Wireless Beacon Frames

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Open `wifi_capture.pcap`. Find all wireless beacon frames and list the SSIDs broadcasting. Also identify the channel and encryption type of the target network `CEH-Lab-WiFi`."

---

## 🎯 Flag Format

```
ssid=<name>; channel=<number>; encryption=<type>
```

Example: `ssid=CEH-Lab-WiFi; channel=6; encryption=WPA2`

---

## 💡 Hints

**Hint 1**

Filter: `wlan.fc.type_subtype == 0x0008` — Beacon frames.

**Hint 2**

`tshark -r wifi_capture.pcap -Y "wlan.fc.type_subtype==8" -T fields -e wlan.ssid -e wlan_radio.channel`

---

## ✅ Solution

```bash
tshark -r wifi_capture.pcap -Y "wlan.fc.type_subtype==8" -T fields -e wlan.ssid -e wlan_radio.channel -e wlan.rsn.pcs.type | sort -u
```

**Answer:** `ssid=CEH-Lab-WiFi; channel=6; encryption=WPA2`

---

## 🤖 Claude Setup Prompt

Provide a pre-built wifi_capture.pcap file or:
```bash
# If wireless adapter available in monitor mode:
airmon-ng start wlan0
airodump-ng wlan0mon -w wifi_capture --output-format pcap
# Capture for 30 seconds, then stop
```

Report back: "Lab ready for Q205 — wifi_capture.pcap with beacon frames ready"
