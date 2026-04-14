# Q005 — Aggressive Scan + NSE Defaults

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run an aggressive scan (`-A`) against Metasploitable and report the **Apache version** and the **vsftpd version** detected.

---

## 🎯 Flag Format

```
apache=<ver>; vsftpd=<ver>
```

Example: `apache=2.2.8; vsftpd=2.3.4`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-A` enables OS detect, version detect, default scripts, traceroute.
</details>

<details>
<summary>Hint 2</summary>

Look for lines `21/tcp` and `80/tcp` in the output.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -A 192.168.52.129
```

Apache **2.2.8** and vsftpd **2.3.4** — the famous backdoored version.

**Answer:** `apache=2.2.8; vsftpd=2.3.4`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q005:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. -A combines -O -sV -sC --traceroute → needs root. Confirm: sudo -n true || sudo -v.
3. vsftpd (port 21) and apache (port 80) must be running for the banners this Q depends on:
   - nc 192.168.52.129 21 → expect "220 (vsFTPd 2.3.4)"
   - curl -sI http://192.168.52.129/ | head -1 → expect "Server: Apache/2.2.8"
   - If down: ssh msfadmin@192.168.52.129 → sudo service vsftpd start; sudo service apache2 start.
4. NSE default scripts must be installed (come with nmap package) — ls /usr/share/nmap/scripts/ | head.

Report back: "Lab ready for Q005 — vsftpd 2.3.4 and Apache 2.2.8 banners confirmed".
```
