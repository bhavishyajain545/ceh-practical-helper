# Q133 — WPA Handshake Crack from PCAP

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🔴 Hard |
| **Tools** | `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Given `wpa.pcap` containing a WPA2 4-way handshake for SSID `LabAP`, crack with aircrack-ng.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=aircrack-ng -w rockyou.txt -e LabAP wpa.pcap`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-e <ESSID>` selects network.
</details>

<details>
<summary>Hint 2</summary>

Need handshake captured.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -e LabAP wpa.pcap
```

Cracks PSK if in dictionary.

**Answer:** `cmd=aircrack-ng -w rockyou.txt -e LabAP wpa.pcap`

📖 Ref: [tools/aircrack-ng.md](../../tools/aircrack-ng.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q133:
1. Conceptual/local Parrot Q — no real wifi.
2. On Parrot: which aircrack-ng; rockyou.txt unzipped.
3. Stage wpa.pcap sample — download test handshake pcap (e.g. aircrack-ng test: curl -o wpa.pcap https://wiki.wireshark.org/SampleCaptures?action=AttachFile&do=get&target=wpa-eap-tls.pcap) or generate own if wifi card present.

Report back: "Lab ready for Q133 — aircrack-ng + rockyou available, wpa.pcap staged".
```
