# AWS Cloud Security: Identity & Access Governance Audit

## 📌 Project Overview
This project demonstrates the engineering and implementation of strict Identity and Access Management (IAM) controls within an Amazon Web Services (AWS) environment. The objective was to secure an Amazon S3 data enclave by enforcing the **Principle of Least Privilege (PoLP)** and **Zero Standing Privileges**, bypassing overly permissive AWS-managed defaults in favor of custom-authored JSON policies.

## 🛠️ Technologies & Services Used
* **Amazon Web Services (AWS)**
* **AWS Identity and Access Management (IAM)**
* **Amazon Simple Storage Service (S3)**
* **JSON (JavaScript Object Notation)**

## 🏗️ Architecture & Implementation

### Phase 1: Establishing the Data Enclave
A secure Amazon S3 bucket was provisioned to act as a simulated repository for sensitive audit logs.
* **Security Posture:** Enforced default `Block All Public Access` settings at the bucket level to mitigate external data exfiltration risks.
* **Asset Population:** Uploaded sample public and confidential data files for access simulation.

### Phase 2: Provisioning the Cloud Identity
Created a dedicated IAM user (`S3-Audit-Analyst`) for programmatic and console access testing. 
* **Security Posture:** The user was deployed with **zero standing privileges**. No default AWS managed policies or group permissions were attached during creation, ensuring a strict "default deny" baseline.

### Phase 3: Engineering the Access Policy
To grant highly restricted access, a custom inline JSON policy was authored and bound to the IAM identity. This policy restricts lateral movement and limits actions to a single resource ARN.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowConsoleNavigation",
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowBucketListing",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME-HERE"
    },
    {
      "Sid": "AllowObjectReadWrite",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME-HERE/*"
    }
  ]
Security Audit & Validation
To verify the efficacy of the access controls, a live architectural audit was conducted using the restricted IAM identity.

✅ Authorized Actions (True Positives):

Successfully navigated the S3 console.

Successfully listed objects within the targeted S3 bucket.

Successfully executed s3:GetObject (download) and s3:PutObject (upload) API calls exclusively within the authorized bucket.

❌ Unauthorized Actions (True Negatives):

Lateral Movement Blocked: Attempted to access the Amazon EC2 dashboard; received explicit Access Denied API errors.

Privilege Escalation Blocked: Attempted to modify the S3 bucket's "Block Public Access" settings; received Access Denied.

Infrastructure Destruction Blocked: Attempted to execute s3:DeleteBucket; action was successfully blocked, proving the identity cannot destroy the storage infrastructure.

<img width="1907" height="868" alt="Screenshot 2026-04-16 122153" src="https://github.com/user-attachments/assets/a8248414-38d1-4db3-a43c-6c95577e5632" />
<img width="1919" height="877" alt="Screenshot 2026-04-16 122205" src="https://github.com/user-attachments/assets/77a760f4-4d63-4266-9c3c-8e1d2f81da1a" />
<img width="1909" height="842" alt="Screenshot 2026-04-16 123942" src="https://github.com/user-attachments/assets/95e387de-f765-4fe4-b375-5e0343074cad" />
<img width="1900" height="864" alt="Screenshot 2026-04-16 124019" src="https://github.com/user-attachments/assets/21664331-4bf5-497e-b0dc-cb4aae25dd2b" />
<img width="1916" height="842" alt="Screenshot 2026-04-16 124121" src="https://github.com/user-attachments/assets/d61fe18b-3c07-4f23-944b-fd8e07d93e09" />
<img width="1658" height="855" alt="Screenshot 2026-04-16 124602" src="https://github.com/user-attachments/assets/5298717f-cbfe-4186-a7ae-27b599fa155e" />
<img width="1895" height="837" alt="Screenshot 2026-04-16 124925" src="https://github.com/user-attachments/assets/a64e0978-4b67-4aaf-91f6-7071481d56eb" />
<img width="1893" height="909" alt="Screenshot 2026-04-16 125544" src="https://github.com/user-attachments/assets/645e00b6-673f-4141-ab0e-2b0c41069348" />
<img width="1891" height="875" alt="Screenshot 2026-04-16 125759" src="https://github.com/user-attachments/assets/5f8600d3-cbb2-4ee2-9252-1b7161d3281a" />
<img width="1878" height="854" alt="Screenshot 2026-04-16 130101" src="https://github.com/user-attachments/assets/d23e86e6-aa2d-4139-aac8-4bb98b6dee6d" />

}
