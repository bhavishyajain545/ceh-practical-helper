# Q077 — TWiki / Tikiwiki Recon

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `curl` |
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
Pre-requisites for Q077:
1. Verify Metasploitable2 (192.168.52.129) up; apache2 running.
2. curl -s http://192.168.52.129/tikiwiki/ must return TikiWiki 1.9.5 HTML.

Report back: "Lab ready for Q077 — TikiWiki 1.9.5 reachable on 192.168.52.129".
```
