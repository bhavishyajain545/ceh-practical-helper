# Q090 — dirb with Larger Wordlist

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Run dirb against Metasploitable using `big.txt`. How many directories found in `/dvwa/`?

---

## 🎯 Flag Format

```
approx_count=<n>
```

Example: `approx_count=10`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`dirb http://<ip>/dvwa/ /usr/share/wordlists/dirb/big.txt`
</details>

<details>
<summary>Hint 2</summary>

DVWA has dirs: vulnerabilities, hackable, dvwa, docs, config, etc.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
dirb http://192.168.52.129/dvwa/ /usr/share/wordlists/dirb/big.txt
```

Approx 10 dirs returned.

**Answer:** `approx_count=10`

📖 Ref: [tools/dirb.md](../../tools/dirb.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q090".
```
