# EC2 Read-Only IAM Policies

This directory contains strict read-only policies for Amazon EC2.

---

## Policies Overview

| File | Policy Name | Description |
| :--- | :--- | :--- |
| `ec2-read-only-policy.json` | `EC2ReadOnlyAccessPolicy` | Grants read-only access to view and describe EC2 instances, security groups, AMIs, volumes, CloudWatch monitoring metrics, and load balancers. **Cannot perform any write, start, stop, launch, reboot, modify, or terminate actions.** |

---

## What the User CAN and CANNOT Do

### Allowed (Read-Only)
- View EC2 instances, status, and system logs (`ec2:Describe*`, `ec2:GetConsoleOutput`, `ec2:GetConsoleScreenshot`)
- View EC2 monitoring metrics, CPU/Network graphs, and alarms in CloudWatch
- View attached EBS volumes, security groups, key pairs, and VPC subnets
- View Auto Scaling groups and Elastic Load Balancers attached to EC2 instances

### Denied (Blocked by Default)
- Starting, stopping, rebooting, or terminating EC2 instances
- Launching new EC2 instances
- Creating, modifying, or deleting security groups, key pairs, or EBS volumes
- Creating, modifying, or deleting tags

---

## Direct AWS CLI Deployment

```bash
aws iam create-policy \
  --policy-name EC2ReadOnlyAccessPolicy \
  --description "Strict read-only access to view EC2 instances and metrics with no modify permissions" \
  --policy-document file://ec2-read-only-policy.json
```

### Attach to a User or Group
```bash
# Attach to an IAM User
aws iam attach-user-policy \
  --user-name <USER_NAME> \
  --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/EC2ReadOnlyAccessPolicy

# Attach to an IAM Group
aws iam attach-group-policy \
  --group-name ReadOnlyAuditors \
  --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/EC2ReadOnlyAccessPolicy
```
