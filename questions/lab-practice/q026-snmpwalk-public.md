# Q026 — SNMP public community

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `snmpwalk -v2c -c public 192.168.52.129` against Metasploitable (port 161). Wait — Metasploitable doesn't run SNMP by default. Confirm via nmap UDP 161 scan and report state.

---

## 🎯 Flag Format

```
snmp_state=<open|closed|filtered>
```

Example: `snmp_state=closed`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`sudo nmap -sU -p 161 <ip>`
</details>

<details>
<summary>Hint 2</summary>

By default Metasploitable does NOT enable SNMP — port closed.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sU -p 161 192.168.52.129
```

Port closed/filtered — confirms no SNMP service.

**Answer:** `snmp_state=closed`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q026:
1. Verify Metasploitable2 (192.168.52.129) reachable.
2. No SNMP service runs by default on Metasploitable2 — do NOT install snmpd; the expected answer is 'closed'.
3. Ensure Parrot can send UDP (no local egress firewall): sudo iptables -L OUTPUT -n.

Report back: "Lab ready for Q026 — no SNMP on 192.168.52.129 (expected closed state)".
```
