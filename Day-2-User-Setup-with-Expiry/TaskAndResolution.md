# TaskAndResolution.md

# ============================================
# PART 1 — TASK & RESOLUTION (LINUX USER SETUP)
# ============================================

## Task

1. Create a user named `mark` on App Server without a home directory.
2. Create a user named `john` on App Server.
3. Set expiry date for `john` to 2027-04-15.
4. Ensure usernames are lowercase.

Server: stapp03 (Stratos Datacenter)  
Access Level: root  

---

## Step 1 — Create Service User Without Home Directory

```bash
useradd -M mark
```

### Explanation

- useradd → Creates new user account
- -M → Prevents creation of home directory
- mark → Username

---

## Verification

```bash
id mark
```

### Output

```bash
uid=1002(mark) gid=1002(mark) groups=1002(mark)
```

### Explanation

- uid=1002 → Unique user ID
- gid=1002 → Primary group ID
- groups=1002 → Assigned group

---

```bash
ls -ld /home/mark
```

### Output

```bash
ls: cannot access '/home/mark': No such file or directory
```

### Explanation

Confirms home directory was NOT created.

---

```bash
grep mark /etc/passwd
```

### Output

```bash
mark:x:1002:1002::/home/mark:/bin/bash
```

### Explanation of Fields

username : password : UID : GID : comment : home : shell

- x → Password stored in /etc/shadow
- /home/mark → Default path (not physically created)
- /bin/bash → Default shell

---

# --------------------------------------------

## Step 2 — Create Temporary User with Expiry

```bash
useradd -e 2027-04-15 john
```

### Explanation

- -e → Sets account expiration date
- 2027-04-15 → Expiry date

---

## Verification

```bash
id john
```

### Output

```bash
uid=1002(john) gid=1002(john) groups=1002(john)
```

---

```bash
chage -l john
```

### Output

```bash
Last password change                                    : Feb 16, 2026
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : Apr 15, 2027
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```

### Explanation

- chage -l → Displays password aging details
- Account expires → Confirms expiry date correctly configured

---

## Final Validation

✔ mark created without home  
✔ john created with expiry  
✔ Verified using id and chage  
✔ Requirements satisfied  

---

# ============================================
# PART 2 — SCENARIO BASED INTERVIEW Q&A (L1 → L4)
# ============================================

# L1 — Junior Level

Q1: How do you create a user with expiry?
A:
```bash
useradd -e YYYY-MM-DD username
```
Explanation: Sets account expiration date.

Q2: How to create user without home?
A:
```bash
useradd -M username
```

Q3: Where is user information stored?
A:
- /etc/passwd
- /etc/shadow
- /etc/group

---

# L2 — System Administrator

Scenario: User cannot login.

Troubleshooting:
1. Check expiry
   ```bash
   chage -l username
   ```
2. Check lock status
   ```bash
   passwd -S username
   ```
3. Check shell
   ```bash
   grep username /etc/passwd
   ```

Possible Root Causes:
- Account expired
- Password expired
- Account locked
- Shell set to /sbin/nologin

---

# L3 — Senior Engineer

Scenario: Need to provision users across 100 servers.

Correct Approach:
- Use Ansible
- Use centralized LDAP
- Avoid manual SSH

Example Ansible:
```yaml
- name: Create user
  user:
    name: john
    expires: 2027-04-15
```

Focus:
- Automation
- Idempotency
- Auditability

---

# L4 — Architect Level

Question: Should we create Linux users in cloud manually?

Answer:
No. Use:
- IAM Roles
- SSM Session Manager
- SSO
- Just-In-Time Access

Architecture Principle:
- Least Privilege
- No static credentials
- Centralized logging

---

# ============================================
# PART 3 — AWS SCENARIO BASED INTERVIEW Q&A
# ============================================

L1 AWS

Q: How to give temporary EC2 access?
A:
- Create IAM user
- Attach policy
- Set expiration using IAM Access Advisor
- Enable MFA

---

L2 AWS

Scenario: Ex-employee accessed EC2.

Root Cause:
- IAM user not disabled
- No MFA
- No password rotation

Fix:
- Enable IAM password policy
- Use IAM roles instead of IAM users

---

L3 AWS

Scenario: EC2 compromised via SSH brute force.

Investigation:
- Security group allowed 0.0.0.0/0 on port 22
- No MFA
- No SSM usage

Prevention:
- Disable SSH
- Use SSM Session Manager
- Restrict Security Groups

---

L4 AWS Architecture

Design secure access model:

- IAM Role based access
- AWS SSO
- CloudTrail logging
- GuardDuty alerts
- Just-In-Time access via IAM Conditions

---

# ============================================
# PART 4 — REAL AWS INCIDENT RCAs
# ============================================

Incident 1 — Production Outage Due to Expired Credentials

Impact:
Application down for 2 hours.

Root Cause:
IAM temporary credentials expired; application lacked refresh logic.

Fix:
- Implement automatic STS refresh
- Monitor credential expiry

Lesson:
Never hardcode temporary credentials.

---

Incident 2 — Security Breach via Open SSH

Impact:
Unauthorized login attempts.

Root Cause:
Security group open to internet.

Fix:
- Restrict SSH
- Use Bastion or SSM
- Enable GuardDuty

---

Incident 3 — Accidental Deletion of System Users

Script:
```bash
userdel $(cat expired.txt)
```

Root Cause:
No UID filtering.

Fix:
```bash
if [ $UID -gt 1000 ]
```

Lesson:
Always protect system accounts.

---

# ============================================
# PART 5 — FULL DEVOPS MOCK INTERVIEW
# ============================================

Round 1 — Linux

Q: Difference between useradd and adduser?
A:
useradd → low-level binary  
adduser → wrapper script  

Q: Explain chage.
A:
Manages password aging policies.

---

Round 2 — AWS

Q: Difference between IAM User and IAM Role?
A:
User → Permanent identity  
Role → Assumed temporary credentials  

---

Round 3 — CI/CD

Q: How do you avoid storing SSH keys in pipeline?
A:
Use IAM roles or OIDC federation.

---

Round 4 — Kubernetes

Q: How to manage access in EKS?
A:
- IAM roles for service accounts
- RBAC policies

---

Round 5 — Architecture

Design secure enterprise access:

- Zero Trust Model
- Centralized Identity Provider
- Automated provisioning via Terraform
- Audit via CloudTrail
- Alerts via CloudWatch + SNS

---

# FINAL SUMMARY

This document covered:

✔ Linux user creation with expiry  
✔ Full verification with command outputs  
✔ L1 to L4 scenario-based interview Q&A  
✔ AWS mapped interview questions  
✔ Real AWS incident RCAs  
✔ Complete DevOps mock interview  

End of Document.

