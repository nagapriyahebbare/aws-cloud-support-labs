# Lab 01 — EC2 Connectivity Troubleshooting

## 🚨 Symptom
Customer reports: *"I launched a new EC2 instance but I cannot SSH into it. The instance shows as Running in the console."*

---

## 🔍 Investigation Steps

### Step 1 — Verify instance state
```bash
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query "Reservations[*].Instances[*].[InstanceId,PublicIpAddress,State.Name]" \
  --output table
```

### Step 2 — Check Security Group inbound rules
```bash
aws ec2 describe-security-groups \
  --group-ids <sg-id> \
  --query "SecurityGroups[*].IpPermissions"
```
Look for: port 22 open to your IP (or `0.0.0.0/0` for testing)

### Step 3 — Check Network ACL
```bash
aws ec2 describe-network-acls \
  --query "NetworkAcls[*].Entries"
```
Look for: inbound rule allowing TCP port 22, outbound rule allowing ephemeral ports (1024–65535)

### Step 4 — Verify Route Table has IGW
```bash
aws ec2 describe-route-tables \
  --query "RouteTables[*].Routes"
```
Look for: `0.0.0.0/0` pointing to an Internet Gateway (`igw-xxxxxx`)

### Step 5 — Confirm public IP assigned
```bash
aws ec2 describe-instances \
  --instance-ids <instance-id> \
  --query "Reservations[*].Instances[*].PublicIpAddress"
```

---

## 🔴 Root Cause
Security Group was missing an inbound rule for **port 22 (SSH)**. The instance had a public IP and a valid IGW route, but the Security Group was blocking all inbound traffic.

---

## ✅ Fix
```bash
aws ec2 authorize-security-group-ingress \
  --group-id <sg-id> \
  --protocol tcp \
  --port 22 \
  --cidr <your-ip>/32
```

---

## 🛡️ Prevention
- Always create Security Groups with **least-privilege rules** from the start
- Never use `0.0.0.0/0` for SSH in production — restrict to your office IP or use a Bastion Host
- Use **AWS Systems Manager Session Manager** as a keyless alternative to SSH

---

## 📚 Related AWS Docs
- [Security Groups for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html)
- [Troubleshoot EC2 connectivity](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html)
