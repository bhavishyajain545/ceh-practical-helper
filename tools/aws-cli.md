# aws-cli — Amazon Web Services CLI

> **Anonymous + authenticated interaction with AWS services.** S3, IAM, EC2, STS, Lambda.

**Install check:** `aws --version`

---

## 🎯 Cheat-flow — S3 first

```bash
aws s3 ls s3://bucket --no-sign-request                 # anonymous list
aws s3 cp s3://bucket/file . --no-sign-request          # anonymous fetch
aws s3 sync s3://bucket ./loot --no-sign-request        # pull everything
aws s3api get-bucket-acl --bucket bucket --no-sign-request
aws s3api get-bucket-policy --bucket bucket --no-sign-request
aws s3api list-objects-v2 --bucket bucket --no-sign-request
```

---

## 🔑 Config / auth

```bash
aws configure                         # prompts for key, secret, region, fmt
# or env vars:
export AWS_ACCESS_KEY_ID=AKIA...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...           # for temp creds
export AWS_DEFAULT_REGION=us-east-1

aws sts get-caller-identity            # "who am I?" — ALWAYS run after finding keys
```

---

## 🔑 Useful commands

| Area | Command |
|---|---|
| **STS** | `aws sts get-caller-identity` → arn, userid, account |
| **IAM** | `aws iam list-users` / `list-roles` / `list-access-keys` |
| IAM | `aws iam get-account-authorization-details` (big dump) |
| IAM | `aws iam list-attached-user-policies --user-name X` |
| **S3** | `aws s3 ls` / `cp` / `sync` / `mb` / `rm` |
| S3 API | `aws s3api get-object --bucket B --key K out.bin` |
| **EC2** | `aws ec2 describe-instances` |
| EC2 | `aws ec2 describe-security-groups` |
| EC2 | `aws ec2 get-console-output --instance-id i-...` |
| **SSM** | `aws ssm describe-parameters` / `get-parameter --name X --with-decryption` |
| **Secrets** | `aws secretsmanager list-secrets` / `get-secret-value --secret-id X` |
| **Lambda** | `aws lambda list-functions` / `get-function --function-name X` |
| **Logs** | `aws logs describe-log-groups` |

---

## 📋 Recipes

```bash
# 1. You found AKIA... creds — what can they do?
export AWS_ACCESS_KEY_ID=AKIA...
export AWS_SECRET_ACCESS_KEY=...
aws sts get-caller-identity                        # confirm valid
aws iam list-attached-user-policies --user-name $(aws sts get-caller-identity --query Arn --output text | cut -d/ -f2)
aws s3 ls                                          # every bucket you can see
aws ec2 describe-instances --query 'Reservations[].Instances[].[InstanceId,PublicIpAddress,State.Name]' --output table

# 2. Read an SSM SecureString parameter
aws ssm get-parameter --name /prod/db/password --with-decryption --query Parameter.Value --output text

# 3. Pull all secrets
for s in $(aws secretsmanager list-secrets --query 'SecretList[].Name' --output text); do
  echo "== $s =="
  aws secretsmanager get-secret-value --secret-id "$s" --query SecretString --output text
done

# 4. S3 anonymous bucket loot
aws s3 sync --no-sign-request s3://target-public ./loot/
```

---

## ⚠️ Gotchas

- `--no-sign-request` = anonymous (no credentials). Use for public buckets.
- Found keys? **Run `sts get-caller-identity` first** — dead keys waste time.
- Watch for `--region` — some commands default differently.
- CloudTrail logs your activity — assume you're being recorded.
- For privilege enumeration beyond IAM, use [pacu](https://github.com/RhinoSecurityLabs/pacu).

---

## 🔗 Related

- [s3scanner](s3scanner.md) · [cloud_enum](cloud_enum.md) · [gsutil](gsutil.md)
