# Q013 — Idle (Zombie) Scan Concept

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2); `192.168.52.130` (Windows 7) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🔴 Hard |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use the Windows 7 host as the **zombie** to scan Metasploitable port 80 via idle scan. Provide the nmap command syntax.

---

## 🎯 Flag Format

```
cmd=<nmap-cmd>
```

Example: `cmd=nmap -sI 192.168.52.130 192.168.52.129 -p 80`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-sI <zombie> <target>` triggers idle scan.
</details>

<details>
<summary>Hint 2</summary>

Zombie must have predictable IPID — Win7 typically OK on isolated lab.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -Pn -sI 192.168.52.130 192.168.52.129 -p 80
```

If IPID is incremental, scan reports port 80 open without source revealing.

**Answer:** `cmd=nmap -sI 192.168.52.130 192.168.52.129 -p 80`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q013:
1. All 3 VMs powered on. Verify from Parrot:
   - ping -c 2 192.168.52.129 (Metasploitable, target)
   - ping -c 2 192.168.52.130 (Win7, zombie) — requires ICMP unblocked on Win7 firewall (see Q010).
2. Idle scan (-sI) requires raw packets → sudo on Parrot. Confirm: sudo -n true || sudo -v.
3. ZOMBIE REQUIREMENT — Win7 must have a predictable (incremental) IPID sequence. Modern OSes often randomise. Quick precheck:
   sudo nmap -O -v 192.168.52.130 | grep -i "IP ID Sequence"
   Want to see "Incremental" or "Broken little-endian incremental". If "Randomized" or "All zeros" → idle scan will not work; pick another zombie (a printer or stripped-down XP VM) or flag this Q as conceptual.
4. Win7 must be IDLE — close browsers, disable Windows Update traffic, no RDP sessions — any background packets bump IPID and corrupt the result. Ideal: freshly booted, no logged-in user activity.
5. Apache on Metasploitable port 80 must be listening (the port we're probing): curl -sI http://192.168.52.129/ | head -1.

Report back: "Lab ready for Q013 — Win7 IPID=Incremental and idle, apache up on Metasploitable:80".
```
