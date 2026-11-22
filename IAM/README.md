# AWS Custom IAM Policies (Dynamic & Zero Configuration)

All policies in this directory use native **AWS IAM Policy Variables** (`${aws:username}`, `${aws:userid}`, and `${aws:PrincipalAccount}`). 

**You do NOT need to replace or provide any variables manually.** 
When attached to any IAM user, AWS automatically resolves their user identity and account ID at runtime.

---

## Policies Overview

| File | Policy Name | Dynamic Resolution Behavior |
| :--- | :--- | :--- |
| `enforce-mfa-policy.json` | `EnforceMFABeforeAccessPolicy` | Dynamically limits credential and MFA self-management to `arn:aws:iam::${aws:PrincipalAccount}:user/${aws:username}` and denies all other actions without MFA. |
| `ec2-instance-operator-policy.json` | `EC2UserOwnedInstanceOperatorPolicy` | Allows describing EC2 instances, but dynamically restricts `StartInstances`, `StopInstances`, and `RebootInstances` to instances tagged with `Owner: ${aws:username}` or `${aws:userid}`. |

---

## How AWS Resolves These Automatically

When a policy is attached to an IAM User (e.g., `alex` in AWS Account `123456789012`):
- `${aws:username}` automatically becomes `alex`
- `${aws:userid}` automatically becomes Alex's unique AWS user ID (e.g. `AIDAXXXXXXXXXXXXXXXXX`)
- `${aws:PrincipalAccount}` automatically becomes `123456789012`

No manual editing or environment configuration is needed.

---

## Direct AWS CLI Deployment (No Placeholders Required)

You can run these commands directly:

### 1. Enforce MFA Policy
```bash
aws iam create-policy \
  --policy-name EnforceMFABeforeAccessPolicy \
  --description "Requires MFA; allows users to manage only their own credentials" \
  --policy-document file://enforce-mfa-policy.json
```

### 2. EC2 User-Owned Operator Policy
```bash
aws iam create-policy \
  --policy-name EC2UserOwnedInstanceOperatorPolicy \
  --description "Allows start/stop/reboot only on instances tagged with user identity" \
  --policy-document file://ec2-instance-operator-policy.json
```

---

## Attaching Directly to Users or Groups

### Attach to a User
```bash
aws iam attach-user-policy \
  --user-name <USER_NAME> \
  --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/EC2UserOwnedInstanceOperatorPolicy
```

### Attach to a Group (All members get isolated permissions automatically)
```bash
aws iam attach-group-policy \
  --group-name Developers \
  --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/EnforceMFABeforeAccessPolicy
```
