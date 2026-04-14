# Q017 — NSE http-enum on Apache

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use NSE script `http-enum` on Metasploitable port 80 and report **one discovered web app path** (any).

---

## 🎯 Flag Format

```
path=</path>
```

Example: `path=/phpMyAdmin/`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap --script http-enum -p 80 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Common discoveries: /phpMyAdmin/, /tikiwiki/, /dvwa/, /mutillidae/.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script http-enum -p 80 192.168.52.129
```

Lists multiple paths; pick any (e.g. /phpMyAdmin/).

**Answer:** `path=/phpMyAdmin/`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Apache + DVWA + phpMyAdmin running on Metasploitable.

Report back: "Lab ready for Q017".
```
