# Q011 — Decoy Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run an nmap scan with **5 decoys** against Metasploitable. Provide the exact flag combo used.

---

## 🎯 Flag Format

```
flag=<nmap-flag>
```

Example: `flag=-D RND:5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-D` lets you spoof source IPs; `RND:N` generates N random decoys.
</details>

<details>
<summary>Hint 2</summary>

Combine with `-sS` for stealth.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sS -D RND:5 192.168.52.129
```

Scan succeeds with 5 random decoy source addresses.

**Answer:** `flag=-D RND:5`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable up.

Report back: "Lab ready for Q011".
```
