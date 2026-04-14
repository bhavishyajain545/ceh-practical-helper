# Q121 — RSA Small Key Factor (Concept)

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🔴 Hard |
| **Tools** | `python3` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Given RSA public key with N=3233, e=17, factor N to find p and q.

---

## 🎯 Flag Format

```
p=<n>; q=<n>
```

Example: `p=53; q=61`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Trial division for tiny N.
</details>

<details>
<summary>Hint 2</summary>

53 * 61 = 3233.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
python3 -c 'n=3233\nfor p in range(2,100):\n  if n%p==0: print(p, n//p); break'
```

p=53, q=61.

**Answer:** `p=53; q=61`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q121:
1. Local Parrot exercise — RSA math only.
2. which python3.

Report back: "Lab ready for Q121 — python3 available".
```
