# Q032 — SMB Protocol Version

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use NSE `smb-protocols` against Metasploitable port 445. Report the **highest SMB dialect** supported.

---

## 🎯 Flag Format

```
smb_dialect=<NT|2.0|3.0>
```

Example: `smb_dialect=NT`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap --script smb-protocols -p 445 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Samba 3.x speaks NT LM 0.12 only.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script smb-protocols -p 445 192.168.52.129
```

Highest dialect = `NT LM 0.12` (SMB1).

**Answer:** `smb_dialect=NT`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q032:
1. Verify Metasploitable2 (192.168.52.129) up; samba running.
2. From Parrot: nmap -p 445 192.168.52.129 — open.
3. No config change — Samba 3.x on Metasploitable2 speaks SMB1 (NT LM 0.12) by default.

Report back: "Lab ready for Q032 — SMB1 dialect exposed on 192.168.52.129:445".
```
