# Q107 — Hashcat with best64 Rules

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🔴 Hard |
| **Tools** | `hashcat` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Apply `best64.rule` to rockyou for NTLM cracking. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=hashcat -m 1000 -r best64.rule hashes.txt rockyou.txt`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Built-in: `/usr/share/hashcat/rules/best64.rule`.
</details>

<details>
<summary>Hint 2</summary>

Rules append numbers/years/etc.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
hashcat -m 1000 -r /usr/share/hashcat/rules/best64.rule hashes.txt /usr/share/wordlists/rockyou.txt
```

Significantly expands keyspace.

**Answer:** `cmd=hashcat -m 1000 -r best64.rule hashes.txt rockyou.txt`

📖 Ref: [tools/hashcat.md](../../tools/hashcat.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q107:
1. Local Parrot exercise.
2. which hashcat; ls /usr/share/hashcat/rules/best64.rule; rockyou.txt unzipped.

Report back: "Lab ready for Q107 — hashcat + best64.rule + rockyou available".
```
