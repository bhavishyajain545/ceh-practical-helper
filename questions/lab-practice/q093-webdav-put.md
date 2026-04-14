# Q093 — Apache WebDAV PUT

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Test if Metasploitable Apache supports `PUT` via WebDAV. Use curl to attempt upload.

---

## 🎯 Flag Format

```
put_allowed=<yes|no>
```

Example: `put_allowed=no`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`curl -X PUT --data 'test' http://<ip>/test.txt -v`
</details>

<details>
<summary>Hint 2</summary>

Returns 405 Method Not Allowed by default.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
curl -X PUT --data 'test' http://192.168.52.129/test.txt -v
```

405 Method Not Allowed.

**Answer:** `put_allowed=no`

📖 Ref: [playbooks/web-servers-playbook.md](../../playbooks/web-servers-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q093:
1. Verify Metasploitable2 up; apache2 running.
2. Metasploitable2 Apache has WebDAV DAV module enabled on /dav (not root) — expected answer 'no' for root. curl -X PUT http://192.168.52.129/test.txt returns 405.

Report back: "Lab ready for Q093 — apache2 up, PUT expected 405 on 192.168.52.129".
```
