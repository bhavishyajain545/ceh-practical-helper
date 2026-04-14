# Q029 — finger Service Probe

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap, finger` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Test the `finger` daemon on Metasploitable port 79. Is it open and what does `finger @192.168.52.129` return?

---

## 🎯 Flag Format

```
port79=<open|closed>; users=<comma-list-or-none>
```

Example: `port79=closed; users=none`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Metasploitable does not run fingerd by default.
</details>

<details>
<summary>Hint 2</summary>

Confirm with `nmap -p 79`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap -p 79 192.168.52.129
finger @192.168.52.129
```

Port closed; finger returns connection refused.

**Answer:** `port79=closed; users=none`

📖 Ref: [tools/finger.md](../../tools/finger.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q029:
1. Verify Metasploitable2 (192.168.52.129) up.
2. Metasploitable2 does NOT run fingerd by default — expected 'closed'. Do not enable it.
3. On Parrot, install finger client if missing: sudo apt install finger.

Report back: "Lab ready for Q029 — port 79 expected closed on 192.168.52.129".
```
