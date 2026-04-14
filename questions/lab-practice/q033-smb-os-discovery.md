# Q033 — smb-os-discovery NSE

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run NSE script `smb-os-discovery` on Win7. Report the **NetBIOS computer name**.

---

## 🎯 Flag Format

```
netbios_name=<NAME>
```

Example: `netbios_name=WIN7-PC`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap --script smb-os-discovery -p 445 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Output includes NetBIOS computer name and domain.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script smb-os-discovery -p 445 192.168.52.130
```

Returns hostname (e.g. `WIN7-PC`).

**Answer:** `netbios_name=WIN7-PC`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q033:
1. Verify Win7 (192.168.52.130) up and reachable.
2. Confirm File and Printer Sharing is enabled on Win7 (so port 445 is open): from Parrot → nmap -p 445 192.168.52.130 — open.
3. Windows Firewall: allow inbound SMB on Private network (or temporarily disable).
4. Note the Win7 hostname Claude will need for flag (e.g. WIN7-PC) — check Control Panel → System.

Report back: "Lab ready for Q033 — Win7 SMB 445 open, smb-os-discovery NSE returns hostname".
```
