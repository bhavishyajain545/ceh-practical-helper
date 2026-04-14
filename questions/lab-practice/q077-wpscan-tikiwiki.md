# Q077 — TWiki / Tikiwiki Recon

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Browse Metasploitable's TikiWiki at `/tikiwiki/` and identify the **version** in the footer.

---

## 🎯 Flag Format

```
version=<x.y>
```

Example: `version=1.9.5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Footer or `tiki-index.php`.
</details>

<details>
<summary>Hint 2</summary>

TikiWiki 1.9.5 → CVE-2007-5423.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
curl -s http://192.168.52.129/tikiwiki/ | grep -i version
```

TikiWiki **1.9.5** — vulnerable to graph_formula RCE.

**Answer:** `version=1.9.5`

📖 Ref: [tools/wpscan.md](../../tools/wpscan.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q077".
```
