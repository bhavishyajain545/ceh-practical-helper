# Q055 — Hashcat NTLM Crack

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashcat` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Crack a sample NTLM hash `b4b9b02e6f09a9bd760f388b67351e2b` (password: `password`) with hashcat. Provide mode and command.

---

## 🎯 Flag Format

```
mode=<n>; pwd=<word>
```

Example: `mode=1000; pwd=password`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Mode 1000 = NTLM.
</details>

<details>
<summary>Hint 2</summary>

`hashcat -m 1000 hash.txt rockyou.txt`
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
echo 'b4b9b02e6f09a9bd760f388b67351e2b' > h.txt
hashcat -m 1000 h.txt /usr/share/wordlists/rockyou.txt
```

Cracks to `password`.

**Answer:** `mode=1000; pwd=password`

📖 Ref: [tools/hashcat.md](../../tools/hashcat.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q055:
1. Local Parrot exercise — no VM interaction.
2. Ensure hashcat installed: which hashcat.
3. Ensure rockyou.txt available: ls /usr/share/wordlists/rockyou.txt (gunzip .gz if needed).
4. If using GPU, confirm drivers (hashcat -I); CPU-only also works: hashcat --force.

Report back: "Lab ready for Q055 — hashcat + rockyou.txt available on Parrot".
```
