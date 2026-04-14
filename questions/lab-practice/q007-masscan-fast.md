# Q007 — masscan Full TCP Sweep

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `masscan` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use `masscan` at rate 1000 to scan **all 65535** TCP ports on Metasploitable. Report the **total open TCP ports**.

---

## 🎯 Flag Format

```
total_open=<n>
```

Example: `total_open=30`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`masscan -p1-65535 --rate=1000 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Compare against nmap top-1000 — masscan finds extra high ports like 6697, 8787, 36213 etc (random RPC).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo masscan -p1-65535 --rate=1000 192.168.52.129
```

Typically ~30 ports across full range (NFS RPC dynamic ports inflate count).

**Answer:** `total_open=30`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q007:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. Confirm masscan installed on Parrot: which masscan → /usr/bin/masscan. If missing: sudo apt install -y masscan.
3. masscan uses its own TCP/IP stack → requires root. Confirm: sudo -n true || sudo -v.
4. masscan can conflict with host firewall conntrack on the sender side. On Parrot, either disable ufw briefly or add an iptables rule to ignore RST on the scan source port. Simplest: sudo iptables -A INPUT -p tcp --dport 60000:61000 -j DROP (matches default masscan source-port range).
5. All Metasploitable services should be up so masscan finds the full ~30 open TCP ports (top 23 + NFS dynamic RPC).

Report back: "Lab ready for Q007 — masscan installed, sudo works, 192.168.52.129 reachable".
```
