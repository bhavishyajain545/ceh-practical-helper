# Q150 — Shodan Recon (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🟢 Easy |
| **Tools** | `shodan CLI (optional) / browser` |
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
Pre-requisites for Q150:
1. Conceptual/recon Q — requires Shodan account + API key for CLI (https://account.shodan.io/).
2. On Parrot optional install: pip3 install shodan; shodan init <APIKEY>.
3. Or just use https://www.shodan.io via browser.
4. Internet access from Parrot required.

Report back: "Lab ready for Q150 — Shodan API key configured or browser access available".
```
