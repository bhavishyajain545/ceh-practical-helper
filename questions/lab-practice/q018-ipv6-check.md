# Q018 — IPv6 Reachability Check

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run `nmap -6` on the link-local IPv6 address of Metasploitable (auto-discovered via `ip -6 neigh`). What flag enables IPv6 in nmap?

---

## 🎯 Flag Format

```
flag=<nmap-flag>
```

Example: `flag=-6`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-6` enables IPv6 mode.
</details>

<details>
<summary>Hint 2</summary>

Find Metasploitable v6 addr via `ping6 -I eth0 ff02::1`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
ping6 -c 2 -I eth0 ff02::1
sudo nmap -6 -sS fe80::XXXX%eth0
```

Scan via IPv6 link-local works.

**Answer:** `flag=-6`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q018:

LAB LIMITATION (READ FIRST): VMware NAT and Host-Only networks DO NOT route IPv6 between guests by default — there is no IPv6 DHCP/RA on the VMware virtual switch. This Q is therefore best treated as either (a) a recall Q for the -6 flag syntax, or (b) a link-local-only exercise over the shared L2 segment. Global/routed IPv6 testing is out of scope for this lab.

1. Confirm Parrot has an IPv6 link-local address on the lab NIC:
   ip -6 addr show eth0 | grep fe80 — should show fe80::.../64.
2. Confirm Metasploitable has an IPv6 link-local address:
   ssh msfadmin@192.168.52.129 → ip -6 addr show eth0 | grep fe80 → note the fe80::xxxx value.
   If no fe80 present, enable IPv6: sudo sysctl -w net.ipv6.conf.eth0.disable_ipv6=0 and ifdown/ifup eth0.
3. From Parrot, discover Metasploitable via ICMPv6 multicast to all-nodes:
   ping6 -c 3 -I eth0 ff02::1 → replies list link-locals of both Metasploitable and host.
   Or: ip -6 neigh show dev eth0 (after the ping).
4. IPv6 scan with nmap requires the explicit -6 flag and zone-id on link-local addrs:
   sudo nmap -6 -sS fe80::XXXX%eth0 (sudo needed for -sS).
5. Primary answer (flag=-6) is syntax recall — does not strictly require a successful scan.

Report back: "Lab ready for Q018 — Parrot and Metasploitable both have fe80:: link-locals; IPv6 routing limitation noted (link-local only)".
```
