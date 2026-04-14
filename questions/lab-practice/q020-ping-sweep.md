# Q020 — Ping Sweep Subnet

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Perform an nmap ping sweep on `192.168.52.0/24`. Report the **count of live hosts** found (excluding broadcast).

---

## 🎯 Flag Format

```
live_hosts=<n>
```

Example: `live_hosts=4`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap -sn 192.168.52.0/24` does ARP/ICMP sweep.
</details>

<details>
<summary>Hint 2</summary>

Should find Parrot, Metasploitable, Win7, possibly the gateway/host.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sn 192.168.52.0/24
```

Typically 3-4 live hosts: attacker, two victims, host machine.

**Answer:** `live_hosts=4`

📖 Ref: [playbooks/scanning-playbook.md](../../playbooks/scanning-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. All 3 VMs powered on.

Report back: "Lab ready for Q020".
```
