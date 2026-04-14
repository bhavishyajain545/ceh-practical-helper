# Q040 — R-Services rsh Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Confirm rsh (port 514) is open on Metasploitable. Use `rsh -l root 192.168.52.129 id` and report the result.

---

## 🎯 Flag Format

```
rsh=<success|fail>
```

Example: `rsh=success`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Metasploitable has rsh-server with `+ +` in /root/.rhosts.
</details>

<details>
<summary>Hint 2</summary>

If `rsh` not installed: `sudo apt install rsh-client`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
rsh -l root 192.168.52.129 id
```

Returns `uid=0(root) gid=0(root)` — full root with no password.

**Answer:** `rsh=success`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q040".
```
