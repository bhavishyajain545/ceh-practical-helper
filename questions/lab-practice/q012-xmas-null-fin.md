# Q012 — Xmas, Null and FIN Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2); `192.168.52.130` (Windows 7) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run a Xmas scan (`-sX`) on Metasploitable port 22 and Windows 7 port 445. Which target classifies the port as **open|filtered** vs **closed**?

---

## 🎯 Flag Format

```
linux=<state>; windows=<state>
```

Example: `linux=open|filtered; windows=closed`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Linux follows RFC 793 — open ports drop Xmas packets (open|filtered).
</details>

<details>
<summary>Hint 2</summary>

Windows replies RST to all → closed.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sX -p 22 192.168.52.129
sudo nmap -sX -p 445 192.168.52.130
```

Linux open|filtered, Windows closed — classic Xmas-scan behavior.

**Answer:** `linux=open|filtered; windows=closed`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q012:
1. Both VMs powered on and reachable from Parrot:
   - ping -c 2 192.168.52.129 (Metasploitable)
   - ping -c 2 192.168.52.130 (Win7 — ICMP must be allowed; see Q010 setup to enable "File and Printer Sharing (Echo Request - ICMPv4-In)" or disable Windows Firewall for the Private profile).
2. Xmas/Null/FIN scans craft non-standard flag combinations → need raw packets → sudo required on Parrot. Confirm: sudo -n true || sudo -v.
3. For the Linux result (open|filtered on port 22), sshd must be listening on Metasploitable:
   - Confirm: nc -vz 192.168.52.129 22 → "succeeded".
4. For the Windows result (closed on port 445), SMB must be listening — same enablement as Q010 (SMBv1 + File and Printer Sharing inbound rule enabled) so Win7 actually sends RST instead of the packet being firewall-dropped (which would give "filtered" instead of "closed").
5. Expected: Linux → open|filtered (RFC 793 compliant, drops packet); Windows → closed (always sends RST).

Report back: "Lab ready for Q012 — both VMs reachable, 22/tcp Metasploitable open, 445/tcp Win7 reachable".
```
