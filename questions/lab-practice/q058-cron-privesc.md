# Q058 — Cron Job Enumeration

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Look at `/etc/crontab` and `/etc/cron.*` on Metasploitable. Report any **world-writable** scripts referenced (any path).

---

## 🎯 Flag Format

```
writable_cron=<path|none>
```

Example: `writable_cron=none`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`ls -la /etc/cron.*` and check perms.
</details>

<details>
<summary>Hint 2</summary>

Stock Metasploitable has no obvious world-writable cron — answer `none` is acceptable.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
cat /etc/crontab
ls -la /etc/cron.*
```

Default — no writable. Acceptable answer `none`.

**Answer:** `writable_cron=none`

📖 Ref: [playbooks/system-hacking-playbook.md](../../playbooks/system-hacking-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q058".
```
