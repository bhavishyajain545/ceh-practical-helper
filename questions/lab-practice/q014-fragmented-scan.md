# Q014 — Fragmented Packets Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use nmap `-f` against Metasploitable to send fragmented probes. What is the default fragment size used by `-f`?

---

## 🎯 Flag Format

```
frag_bytes=<n>
```

Example: `frag_bytes=8`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-f` fragments to 8-byte chunks; `-ff` to 16.
</details>

<details>
<summary>Hint 2</summary>

Custom MTU via `--mtu <n>` (multiple of 8).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -f -sS 192.168.52.129
```

Default is **8 bytes** per fragment.

**Answer:** `frag_bytes=8`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q014:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. -f fragments at IP layer → needs raw packets → sudo on Parrot. Confirm: sudo -n true || sudo -v.
3. On Parrot, make sure no egress firewall / iptables rule drops non-first fragments:
   - Check: sudo iptables -L OUTPUT -v -n | grep -i frag
   - If any frag-related DROP exists, temporarily flush (lab-only): sudo iptables -F OUTPUT.
4. Metasploitable kernel (2.6) reassembles fragments fine out of the box — no sysctl tuning needed on target.
5. This Q is a recall/syntax Q (answer = 8 bytes). The scan itself is optional but nice to verify fragmentation actually happens: sudo tcpdump -i eth0 -n "host 192.168.52.129 and ip[6:2] & 0x3fff != 0" in another terminal while running the nmap -f scan.

Report back: "Lab ready for Q014 — 192.168.52.129 reachable, sudo works, no frag-drop rules on Parrot".
```
