# Q133 — WPA Handshake Crack from PCAP

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🔴 Hard |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q133".
```
