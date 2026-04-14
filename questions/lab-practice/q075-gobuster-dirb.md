# Q075 — gobuster Directory Brute

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `gobuster` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use gobuster on `http://192.168.52.129/` with `dirb common.txt`. Report **two** discovered paths.

---

## 🎯 Flag Format

```
paths=<p1>,<p2>
```

Example: `paths=/dvwa,/phpMyAdmin`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`gobuster dir -u <url> -w wordlist`
</details>

<details>
<summary>Hint 2</summary>

Common.txt comes with dirb.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
gobuster dir -u http://192.168.52.129/ -w /usr/share/wordlists/dirb/common.txt
```

Finds /dvwa, /phpMyAdmin, /mutillidae, /tikiwiki, /test, /twiki etc.

**Answer:** `paths=/dvwa,/phpMyAdmin`

📖 Ref: [tools/gobuster.md](../../tools/gobuster.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q075:
1. Verify Metasploitable2 (192.168.52.129) up; apache2 running.
2. On Parrot: which gobuster (sudo apt install gobuster if missing).
3. Wordlist present: ls /usr/share/wordlists/dirb/common.txt (install dirb package if missing).

Report back: "Lab ready for Q075 — apache2 up on 192.168.52.129, gobuster + dirb wordlist available".
```
