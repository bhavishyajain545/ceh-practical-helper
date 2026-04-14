# Q100 — Hashcat Mode Identification

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashcat` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Identify hashcat mode for **NetNTLMv2**. Provide mode number.

---

## 🎯 Flag Format

```
mode=<n>
```

Example: `mode=5600`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Run `hashcat --help | grep -i ntlm`.
</details>

<details>
<summary>Hint 2</summary>

Common: 1000=NTLM, 5500=NetNTLMv1, 5600=NetNTLMv2, 1800=sha512crypt.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hashcat --help | grep -i 'NetNTLMv2'
```

Mode 5600 for NetNTLMv2.

**Answer:** `mode=5600`

📖 Ref: [tools/hashcat.md](../../tools/hashcat.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q100:
1. Local Parrot exercise — just hashcat --help.
2. which hashcat.

Report back: "Lab ready for Q100 — hashcat installed on Parrot".
```
