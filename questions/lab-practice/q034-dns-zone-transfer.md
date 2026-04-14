# Q034 — DNS Zone Transfer Attempt (lab)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nmap, dig` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `dig @192.168.52.129 axfr example.local` against Metasploitable's DNS (if running). Report whether transfer succeeds.

---

## 🎯 Flag Format

```
axfr=<allowed|refused|no_dns>
```

Example: `axfr=no_dns`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Metasploitable doesn't run a DNS server by default.
</details>

<details>
<summary>Hint 2</summary>

Confirm port 53 TCP state with nmap.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap -p 53 192.168.52.129
dig @192.168.52.129 axfr example.local
```

Port 53 may be open via dnsmasq fragments — usually no zone available; expect `no_dns` or refused.

**Answer:** `axfr=no_dns`

📖 Ref: [tools/dig.md](../../tools/dig.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q034:
1. Verify Metasploitable2 (192.168.52.129) up.
2. Metasploitable2 does NOT host an authoritative DNS zone — expected answer 'no_dns' / refused. Do not add named zones.
3. On Parrot: which dig (install dnsutils/bind9-dnsutils if missing).

Report back: "Lab ready for Q034 — no AXFR zone on 192.168.52.129 (expected refused)".
```
