# Q148 — SET Credential Harvester (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hping3`, `hulk`, `setoolkit`, `bettercap`, `shodan` |
| **Time budget** | 10–20 min |

---

## 📝 Question

In setoolkit, navigate menu to clone `http://www.facebook.com` and host harvester. Provide menu path (numbers).

---

## 🎯 Flag Format

```
menu_path=<n.n.n>
```

Example: `menu_path=1.2.3`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

1) Social-Engineering Attacks → 2) Website Attack Vectors → 3) Credential Harvester → 2) Site Cloner.
</details>

<details>
<summary>Hint 2</summary>

Then enter URL.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo setoolkit
# 1 → 2 → 3 → 2 → enter LHOST → enter URL
```

SET clones the page and serves harvester.

**Answer:** `menu_path=1.2.3.2`

📖 Ref: [playbooks/social-engineering-playbook.md](../../playbooks/social-engineering-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q148".
```
