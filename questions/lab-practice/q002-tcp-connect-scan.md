# Q002 — TCP Connect Scan vs SYN Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run a full TCP connect scan (`-sT`) against Metasploitable and report the **number of open ports** plus the **highest port number** found open.

---

## 🎯 Flag Format

```
open=<n>; highest=<port>
```

Example: `open=23; highest=8180`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap -sT` does a full 3-way handshake — slower but no root needed.
</details>

<details>
<summary>Hint 2</summary>

Sort the output or look at the last line of the open-ports list to find the highest port.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap -sT 192.168.52.129
```

Default top-1000 scan reveals 23 open ports; highest commonly **8180** (Tomcat).

**Answer:** `open=23; highest=8180`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q002:
1. Verify Metasploitable2 (192.168.52.129) is reachable from Parrot: ping -c 2 192.168.52.129.
2. Confirm nmap on Parrot supports -sT (default, no root needed): which nmap → /usr/bin/nmap; nmap --version.
3. All 23 default Metasploitable TCP services should be up. Optional sanity: ssh msfadmin@192.168.52.129 (password: msfadmin) → sudo service --status-all | grep '+'.
4. If any listener is down, start it: sudo service <name> start (ssh, apache2, vsftpd, postfix, samba, tomcat6, mysql, postgresql).

Report back: "Lab ready for Q002 — 192.168.52.129 reachable, nmap present".
```
