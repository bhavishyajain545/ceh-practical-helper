# Q021 — Enum4linux SMB Shares

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `enum4linux -a` on Metasploitable and identify the share named for **temporary files** and the share for **printer drivers**.

---

## 🎯 Flag Format

```
tmp_share=<name>; printer_share=<name>
```

Example: `tmp_share=tmp; printer_share=print$`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`enum4linux -a <ip>` runs everything.
</details>

<details>
<summary>Hint 2</summary>

Look under `Share Enumeration`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
enum4linux -a 192.168.52.129
```

Shares: print$, tmp, opt, IPC$, ADMIN$.

**Answer:** `tmp_share=tmp; printer_share=print$`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q021".
```
