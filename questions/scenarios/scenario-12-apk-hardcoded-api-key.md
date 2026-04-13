# Scenario 12 — APK Reverse to Hardcoded API Key

**Difficulty:** 🟡 | **Time:** ~25 min | **Domains:** 15 Mobile, 12 Web Apps
**Tools:** `apktool`, `jadx`, `curl`, `strings`

## Story
You're given `ceh-bank.apk`. A developer hardcoded an API key. Use it to query `https://api.ceh-bank.local/flag` and retrieve the flag.

## Step 1 — Quick triage
```bash
file ceh-bank.apk
unzip -l ceh-bank.apk | head
strings ceh-bank.apk | grep -iE 'api|key|token|secret' | head
```
**Expected finding:** maybe a match in raw strings if keys live in resources.

## Step 2 — Decode resources
```bash
apktool d ceh-bank.apk -o ceh-bank
ls ceh-bank/res/values/
grep -rni 'api_key\|apikey\|token' ceh-bank/res/
```
**Expected finding:** `strings.xml` contains `<string name="api_key">sk_live_abcd1234...</string>`.

## Step 3 — Decompile Java (if not in resources)
```bash
jadx -d src-out ceh-bank.apk
grep -rni 'api_key\|Bearer\|Authorization' src-out/ | head
```
**Expected finding:** `String API_KEY = "sk_live_abcd1234";` in `MainActivity.java`.

## Step 4 — Check AndroidManifest for endpoints
```bash
cat ceh-bank/AndroidManifest.xml | grep -i 'android:host\|http'
grep -rni 'https://' src-out/ | head
```
**Expected finding:** base URL `https://api.ceh-bank.local/`.

## Step 5 — Query the API
```bash
curl -H "Authorization: Bearer sk_live_abcd1234" https://api.ceh-bank.local/flag
# or
curl -H "X-API-Key: sk_live_abcd1234" https://api.ceh-bank.local/flag
```
**Expected finding:** JSON response containing flag.

## Step 6 — Final answer
**Answer format:** the flag string from the API response.

## Gotchas across this chain
- Try `strings` first — if the key is in `res/values/strings.xml` you're done in 10 seconds.
- `jadx` is better than `dex2jar + jd-gui` for modern APKs — fewer decode errors.
- Auth header scheme varies: `Bearer`, `X-API-Key`, `apikey=` query param — try all three.
- `apktool` and `jadx` answer different questions: apktool = resources/manifest, jadx = code.
- Some keys are base64-encoded — `echo <key> | base64 -d` before using.

## Variant questions this scenario teaches you to handle
- "Extract strings from this APK" → step 2.
- "What API endpoint does the app call?" → step 4.
- "Find the hardcoded API key" → step 2 or 3.
- "Retrieve the flag from the API" → full chain.
