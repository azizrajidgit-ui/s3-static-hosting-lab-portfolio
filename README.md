# AWS S3 Bucket Provisioning & Public Object Access — LAB-184

![AWS](https://img.shields.io/badge/AWS-S3-232F3E?logo=amazonwebservices&logoColor=FF9900)
![Domain](https://img.shields.io/badge/Domain-Cloud_Storage-blue)
![Focus](https://img.shields.io/badge/Focus-Access_Control-purple)

Hands-on cloud lab: provision a secure, partially public S3 bucket using the AWS CLI and Console, applying least-privilege access at the object level.

Independent learning project — not affiliated with or endorsed by AWS.

---

## Objectives

- Provision an S3 bucket via the AWS CLI
- Enable object-level (not bucket-level) public read access
- Configure ACLs with the bucket-owner-preferred setting
- Verify access from a browser and audit contents programmatically

## Architecture / Workflow

```
EC2 Instance Connect (Linux CLI host)
        │
        ▼
   AWS CLI  (aws configure → region: us-west-2, output: json)
        │
        ├─ 1. Create bucket           →  aws s3 mb
        ├─ 2. Disable Block Public Access (bucket level)
        ├─ 3. Enable ACLs             →  bucket-owner-preferred
        ├─ 4. Upload object           →  grant public-read on object only
        └─ 5. Verify                  →  browser URL test + aws s3 ls
```

## Implementation

### 1. CLI Setup

Connected to a Linux EC2 instance via EC2 Instance Connect and configured the AWS CLI:

```bash
aws configure
# AWS Access Key ID / Secret Access Key
# Default region: us-west-2
# Default output format: json
```

### 2. Create the Bucket

```bash
aws s3 mb s3://<bucket-name> --region us-west-2
```

### 3. Configure Access Control

In the S3 Console under the bucket's Permissions tab:

1. Turn off **Block all public access** (scoped to this bucket only)
2. Under Object Ownership, enable **ACLs** with **bucket owner (preferred)**
3. Leave the bucket policy unchanged — public access is granted per-object, not bucket-wide

### 4. Upload & Verify

Upload the object through the Console and grant public-read on that single object:

```bash
# Verify via browser or curl
curl -I https://<bucket-name>.s3.us-west-2.amazonaws.com/<object-key>

# Audit bucket contents via CLI
aws s3 ls s3://<bucket-name>
```

## Access Control Summary

| Layer | Public? | Reason |
|---|---|---|
| Account | Blocked | Account-level guardrail stays on |
| Bucket | Private | No bucket policy granting public access |
| Object | Public read (per-object) | Least-privilege exposure |

## Key Takeaway

Public access in S3 should be scoped as narrowly as possible. Disabling account/bucket-level blocks is only the first step — pairing it with object-level ACLs (rather than a blanket bucket policy) keeps the rest of the bucket private by default while exposing only the intended objects.

## Skills Demonstrated

- **AWS CLI** — profile configuration, bucket creation, object listing
- **Amazon S3** — block public access settings, ACLs, object ownership
- **Security fundamentals** — principle of least privilege applied to storage
- **Console + CLI parity** — validating infrastructure changes through multiple interfaces

---

## License

MIT — see [LICENSE](LICENSE).
