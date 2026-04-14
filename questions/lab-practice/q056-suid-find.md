# Q056 — SUID Binary Hunt

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `find (bash)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On Metasploitable, find all SUID binaries. Report the count and one **GTFOBins-exploitable** binary present (e.g. `nmap`).

---

## 🎯 Flag Format

```
suid_count=<n>; gtfobin=<name>
```

Example: `suid_count=27; gtfobin=nmap`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`find / -perm -4000 -type f 2>/dev/null`
</details>

<details>
<summary>Hint 2</summary>

Metasploitable has old `nmap` with `--interactive` mode for shell escape.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
find / -perm -4000 -type f 2>/dev/null | wc -l
find / -perm -4000 -type f 2>/dev/null | grep nmap
```

~27 SUIDs; `/usr/bin/nmap` interactive escape: `nmap --interactive` → `!sh`.

**Answer:** `suid_count=27; gtfobin=nmap`

📖 Ref: [playbooks/system-hacking-playbook.md](../../playbooks/system-hacking-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q056:
1. Need shell on Metasploitable2 as any user (ssh msfadmin@192.168.52.129 / msfadmin).
2. No setup — SUID-set binaries are baked into Metasploitable2 install.

Report back: "Lab ready for Q056 — shell on 192.168.52.129 available".
```
