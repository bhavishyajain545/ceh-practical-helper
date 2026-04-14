# Q039 — Tomcat Manager Version

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Browse `http://192.168.52.129:8180/` and identify the **Tomcat version**.

---

## 🎯 Flag Format

```
tomcat=<x.y.z>
```

Example: `tomcat=5.5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Default page or manager footer shows version.
</details>

<details>
<summary>Hint 2</summary>

Or `curl -I http://192.168.52.129:8180/` Server header.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
curl -s http://192.168.52.129:8180/ | grep -i 'tomcat'
```

Apache Tomcat **5.5** with default creds tomcat:tomcat.

**Answer:** `tomcat=5.5`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q039".
```
