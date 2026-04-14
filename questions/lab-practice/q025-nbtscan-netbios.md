# Q025 — NetBIOS Name Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `nbtscan` against `192.168.52.0/24`. Report the **NetBIOS name** of the Metasploitable host.

---

## 🎯 Flag Format

```
netbios=<name>
```

Example: `netbios=METASPLOITABLE`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nbtscan 192.168.52.0/24`
</details>

<details>
<summary>Hint 2</summary>

NetBIOS names are uppercase.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nbtscan 192.168.52.0/24
```

Returns `METASPLOITABLE` for `.129`.

**Answer:** `netbios=METASPLOITABLE`

📖 Ref: [tools/nbtscan.md](../../tools/nbtscan.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q025".
```
