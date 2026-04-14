# Q005 — Aggressive Scan + NSE Defaults

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run an aggressive scan (`-A`) against Metasploitable and report the **Apache version** and the **vsftpd version** detected.

---

## 🎯 Flag Format

```
apache=<ver>; vsftpd=<ver>
```

Example: `apache=2.2.8; vsftpd=2.3.4`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-A` enables OS detect, version detect, default scripts, traceroute.
</details>

<details>
<summary>Hint 2</summary>

Look for lines `21/tcp` and `80/tcp` in the output.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -A 192.168.52.129
```

Apache **2.2.8** and vsftpd **2.3.4** — the famous backdoored version.

**Answer:** `apache=2.2.8; vsftpd=2.3.4`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable up; httpd + vsftpd running.

Report back: "Lab ready for Q005".
```
