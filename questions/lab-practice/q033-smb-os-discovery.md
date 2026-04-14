# Q033 — smb-os-discovery NSE

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q033".
```
