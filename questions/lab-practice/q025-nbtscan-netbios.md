# Q025 — NetBIOS Name Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nbtscan` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `nbtscan` against `192.168.52.0/24`. Report the **NetBIOS name** of the Metasploitable host.

---

## 🎯 Flag Format

```
netbios=<name>
```

Example: `netbios=METASPLOITABLE`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nbtscan 192.168.52.0/24`
</details>

<details>
<summary>Hint 2</summary>

NetBIOS names are uppercase.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nbtscan 192.168.52.0/24
```

Returns `METASPLOITABLE` for `.129`.

**Answer:** `netbios=METASPLOITABLE`

📖 Ref: [tools/nbtscan.md](../../tools/nbtscan.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q025:
1. Verify Metasploitable2 (192.168.52.129) up; nmbd running (sudo service nmbd status).
2. UDP 137 must be open on target: nmap -sU -p 137 192.168.52.129.
3. On Parrot, install nbtscan if missing: sudo apt install nbtscan.

Report back: "Lab ready for Q025 — nbtscan installed, UDP 137 reachable on 192.168.52.129".
```
