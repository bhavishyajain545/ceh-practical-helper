# Q150 — Shodan Recon (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hping3`, `hulk`, `setoolkit`, `bettercap`, `shodan` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Provide Shodan search query for finding internet-exposed devices banner-matching `vsftpd 2.3.4`.

---

## 🎯 Flag Format

```
query=<text>
```

Example: `query=vsftpd 2.3.4`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Shodan banner search.
</details>

<details>
<summary>Hint 2</summary>

Add `country:IN` etc. to filter.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# shodan search 'vsftpd 2.3.4'
```

Returns global hosts running the backdoored vsftpd.

**Answer:** `query=vsftpd 2.3.4`

📖 Ref: [playbooks/recon-playbook.md](../../playbooks/recon-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q150".
```
