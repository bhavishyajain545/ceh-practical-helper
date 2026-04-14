# Q001 — Identify Open Ports on Metasploitable

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–8 min |

---

## 📝 Question

Perform a TCP SYN scan on the target and identify **how many TCP ports** are open in the top 1000 ports. Also list the port number running the **SSH** service.

---

## 🎯 Flag Format

```
open_ports=<number>; ssh_port=<number>
```

Example: `open_ports=23; ssh_port=22`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Use `nmap -sS` for SYN scan. Default scans top 1000 ports.
</details>

<details>
<summary>Hint 2</summary>

Service version detection ke liye `-sV` flag add karo — SSH port confirm hoga.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sS -sV 192.168.52.129
```

Metasploitable2 pe typically **23 ports** open hote hain (21, 22, 23, 25, 53, 80, 111, 139, 445, 512, 513, 514, 1099, 1524, 2049, 2121, 3306, 5432, 5900, 6000, 6667, 8009, 8180). SSH port **22**.

**Answer:** `open_ports=23; ssh_port=22`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Metasploitable2 VM (192.168.52.129) should already be running in VMware. Verify:

1. From Parrot (192.168.52.128), ping 192.168.52.129 — must succeed.
2. SSH into msfadmin@192.168.52.129 (password: msfadmin) and confirm default services are up:
   - sudo service ssh status
   - sudo service vsftpd status
   - sudo service apache2 status
   - sudo service samba status
3. If any service is down, start it: `sudo service <name> start`
4. No additional setup needed — Metasploitable2 ships with ~23 open ports by default.

Report back: "Lab ready for Q001 — 23 ports expected open on 192.168.52.129".
```
