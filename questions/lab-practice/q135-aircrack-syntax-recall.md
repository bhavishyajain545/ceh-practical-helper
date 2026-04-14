# Q135 — aircrack-ng Syntax Recall

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Provide aircrack-ng command to crack WEP with IV file `ivs.cap`.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=aircrack-ng ivs.cap`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-a 1` forces WEP, `-a 2` WPA.
</details>

<details>
<summary>Hint 2</summary>

Default auto-detect works.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
aircrack-ng -a 1 ivs.cap
```

Recovers WEP key from collected IVs.

**Answer:** `cmd=aircrack-ng -a 1 ivs.cap`

📖 Ref: [tools/aircrack-ng.md](../../tools/aircrack-ng.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q135:
1. Syntax recall Q — no live wifi needed.
2. which aircrack-ng.

Report back: "Lab ready for Q135 — aircrack-ng available".
```
