# Q020 — Ping Sweep Subnet

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
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
Pre-requisites for Q020:
1. All lab VMs must be powered on and attached to the 192.168.52.0/24 VMware segment (VMnet8 NAT or the dedicated host-only):
   - Parrot (192.168.52.128)
   - Metasploitable2 (192.168.52.129)
   - Win7 (192.168.52.130) — and Windows Firewall must allow ICMP Echo (see Q010 setup), else Win7 will be missed by the sweep.
2. -sn on a local /24 uses ARP by default for same-subnet hosts (very reliable — doesn't care about ICMP filtering). Needs sudo for ARP: sudo -n true || sudo -v.
3. Confirm Parrot's lab IP/netmask: ip -4 addr show eth0 | grep 192.168.52. — must show 192.168.52.128/24.
4. Expect 3-4 responders: Parrot itself, Metasploitable, Win7, and typically the VMware NAT gateway (192.168.52.2) and DHCP (192.168.52.254). Count may be 4-5 depending on which VMnet is used.

Report back: "Lab ready for Q020 — Parrot on 192.168.52.128/24, other VMs up, sudo works".
```
