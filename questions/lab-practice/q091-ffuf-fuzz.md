# Q091 — ffuf Parameter Fuzzing

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `ffuf` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Fuzz parameter names on Mutillidae index.php using ffuf. Provide command syntax.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=ffuf -u http://192.168.52.129/mutillidae/index.php?FUZZ=test -w params.txt`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`FUZZ` keyword is the placeholder.
</details>

<details>
<summary>Hint 2</summary>

Use a parameter wordlist (Seclists).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
ffuf -u 'http://192.168.52.129/mutillidae/index.php?FUZZ=test' -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -mc 200
```

Identifies valid parameter names.

**Answer:** `cmd=ffuf -u http://192.168.52.129/mutillidae/index.php?FUZZ=test -w params.txt`

📖 Ref: [tools/ffuf.md](../../tools/ffuf.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q091:
1. Verify Metasploitable2 up; Mutillidae reachable.
2. On Parrot install ffuf: which ffuf (sudo apt install ffuf).
3. Seclists wordlist: ls /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt (sudo apt install seclists).

Report back: "Lab ready for Q091 — Mutillidae reachable, ffuf + seclists installed".
```
