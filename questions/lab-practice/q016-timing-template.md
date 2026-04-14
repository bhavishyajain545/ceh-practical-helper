# Q016 — Timing Template T4

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run an aggressive-timing nmap scan (T4) against Metasploitable. Report scan duration in seconds (approx).

---

## 🎯 Flag Format

```
timing=<T0-T5>; rough_seconds=<n>
```

Example: `timing=T4; rough_seconds=15`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-T4` is aggressive but reliable on LAN.
</details>

<details>
<summary>Hint 2</summary>

Time appears at end: `Nmap done: 1 IP address ... scanned in X.Xs`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -T4 -sS 192.168.52.129
```

On LAN, completes in ~10-20s.

**Answer:** `timing=T4; rough_seconds=15`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable up.

Report back: "Lab ready for Q016".
```
