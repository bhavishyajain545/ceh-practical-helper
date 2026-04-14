# Q078 — phpMyAdmin Default Login

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `curl (or browser)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Login to Metasploitable phpMyAdmin at `/phpMyAdmin/`. What credentials work?

---

## 🎯 Flag Format

```
user=<u>; pass=<p>
```

Example: `user=root; pass=`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Blank root password.
</details>

<details>
<summary>Hint 2</summary>

Try root with no password.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# http://192.168.52.129/phpMyAdmin/ → user=root, pass=(blank)
```

Logs in as root.

**Answer:** `user=root; pass=`

📖 Ref: [tools/default-creds.md](../../tools/default-creds.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q078:
1. Verify Metasploitable2 (192.168.52.129) up; apache2+mysql running.
2. curl http://192.168.52.129/phpMyAdmin/ returns 200.
3. mysql root has blank password (Metasploitable2 default — do not change).

Report back: "Lab ready for Q078 — phpMyAdmin reachable, mysql root blank on 192.168.52.129".
```
