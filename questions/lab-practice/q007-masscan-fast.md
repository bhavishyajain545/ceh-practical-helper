# Q007 — masscan Full TCP Sweep

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use `masscan` at rate 1000 to scan **all 65535** TCP ports on Metasploitable. Report the **total open TCP ports**.

---

## 🎯 Flag Format

```
total_open=<n>
```

Example: `total_open=30`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`masscan -p1-65535 --rate=1000 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Compare against nmap top-1000 — masscan finds extra high ports like 6697, 8787, 36213 etc (random RPC).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo masscan -p1-65535 --rate=1000 192.168.52.129
```

Typically ~30 ports across full range (NFS RPC dynamic ports inflate count).

**Answer:** `total_open=30`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable up.
2. Install masscan if missing: `sudo apt install masscan`.

Report back: "Lab ready for Q007".
```
