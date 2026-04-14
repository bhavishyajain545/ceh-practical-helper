# Q110 — Password Policy via rpcclient

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `rpcclient` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use rpcclient `getdompwinfo` against Metasploitable to read password policy. Report **min password length**.

---

## 🎯 Flag Format

```
min_length=<n>
```

Example: `min_length=5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`rpcclient -U "" -N 192.168.52.129 -c 'getdompwinfo'`
</details>

<details>
<summary>Hint 2</summary>

Default Samba returns small min length.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
rpcclient -U "" -N 192.168.52.129 -c 'getdompwinfo'
```

Min Password Length: 5 (default).

**Answer:** `min_length=5`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q110:
1. Verify Metasploitable2 up; samba running; null session allowed.
2. which rpcclient.

Report back: "Lab ready for Q110 — SMB null session on 192.168.52.129, rpcclient ready".
```
