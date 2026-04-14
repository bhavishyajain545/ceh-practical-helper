# Q015 — Source Port Manipulation

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Some firewalls allow port 53 (DNS) outbound. Run an nmap scan from source port 53 against Metasploitable.

---

## 🎯 Flag Format

```
flag=<nmap-flag>
```

Example: `flag=--source-port 53`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-g 53` or `--source-port 53`.
</details>

<details>
<summary>Hint 2</summary>

Useful to bypass simple ACL firewalls.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap --source-port 53 -sS 192.168.52.129
```

Scan completes using src port 53.

**Answer:** `flag=--source-port 53`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable up.

Report back: "Lab ready for Q015".
```
