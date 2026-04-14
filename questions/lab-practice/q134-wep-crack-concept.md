# Q134 — WEP Crack Conceptual Steps

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

List the four aircrack-ng tools used in WEP cracking workflow.

---

## 🎯 Flag Format

```
tools=<comma-list>
```

Example: `tools=airmon-ng,airodump-ng,aireplay-ng,aircrack-ng`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Mon mode → capture IVs → inject → crack.
</details>

<details>
<summary>Hint 2</summary>

WEP is broken even at 64-bit.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Workflow only — no wifi card in lab
```

airmon-ng, airodump-ng, aireplay-ng, aircrack-ng.

**Answer:** `tools=airmon-ng,airodump-ng,aireplay-ng,aircrack-ng`

📖 Ref: [tools/aircrack-ng.md](../../tools/aircrack-ng.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q134".
```
