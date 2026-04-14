# Q039 — Tomcat Manager Version

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `curl` |
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
Pre-requisites for Q039:
1. Verify Metasploitable2 (192.168.52.129) up; tomcat running: ssh msfadmin@192.168.52.129 → sudo service tomcat55 status (start with sudo service tomcat55 start if down).
2. From Parrot: curl -sI http://192.168.52.129:8180/ — expect 200 OK with Tomcat server header.

Report back: "Lab ready for Q039 — Tomcat 5.5 up on 192.168.52.129:8180".
```
