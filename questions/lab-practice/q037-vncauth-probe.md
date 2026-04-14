# Q037 — VNC Auth Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap, vncviewer` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Probe Metasploitable VNC (port 5900) and identify the **auth type**. Then login with password `password`.

---

## 🎯 Flag Format

```
auth_type=<type>; login=<success|fail>
```

Example: `auth_type=VNC; login=success`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap --script vnc-info -p 5900 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Use vncviewer with password `password`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script vnc-info -p 5900 192.168.52.129
vncviewer 192.168.52.129
```

Auth = VNC; password `password` logs in as root.

**Answer:** `auth_type=VNC; login=success`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q037:
1. Verify Metasploitable2 (192.168.52.129) up; vnc4server/x11vnc running (sudo service vnc status or ps aux | grep vnc).
2. From Parrot: nmap -p 5900 192.168.52.129 — open.
3. Password is 'password' (default). On Parrot install vncviewer if needed: sudo apt install tigervnc-viewer.

Report back: "Lab ready for Q037 — VNC 5900 open on 192.168.52.129, password='password'".
```
