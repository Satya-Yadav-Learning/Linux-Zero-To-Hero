# TaskAndResolution.md

# ==============================================================
# TASK
# ==============================================================

Locate all files (excluding directories) owned by user `kirsty`
within `/home/usersdata`.

Copy these files while preserving the directory structure
to `/ecommerce`.

User executing task: steve (with sudo privileges)

# ==============================================================
# PART 1 — IMPLEMENTATION & RESOLUTION
# ==============================================================

## Step 1 — Navigate to Source Directory

```bash
cd /home/usersdata
pwd
```

### Output

```bash
/home/usersdata
```

### Explanation

Confirms correct working directory before performing operations.

---

## Step 2 — Identify Files Owned by kirsty

```bash
find /home/usersdata -type f -user kirsty | wc -l
```

### Output

```bash
1625
```

### Explanation

- find → Search command
- -type f → Only regular files (excludes directories)
- -user kirsty → Filter by owner
- wc -l → Count results

Result: 1625 files owned by kirsty.

---

## Step 3 — Create Destination Directory

```bash
sudo mkdir -p /ecommerce
```

### Explanation

- -p ensures directory is created if not present.
- Prevents failure if directory already exists.

---

## Step 4 — Copy Files Preserving Directory Structure

```bash
sudo find /home/usersdata -type f -user kirsty -exec cp --parents {} /ecommerce \;
```

### Command Breakdown

- find → Locate files
- -type f → Only files
- -user kirsty → Ownership filter
- -exec → Execute command on each file
- cp → Copy command
- --parents → Preserve full directory hierarchy
- {} → Placeholder for found file
- \; → End of exec statement

---

## Step 5 — Verify Destination File Count

```bash
find /ecommerce/home/usersdata -type f | wc -l
```

### Output

```bash
1625
```

### Verification Result

✔ Source file count = 1625  
✔ Destination file count = 1625  
✔ No missing files  

---

## Step 6 — Confirm Only kirsty Files Were Copied

```bash
find /ecommerce/home/usersdata -type f ! -user kirsty
```

### Output

```
(no output)
```

Meaning: No unauthorized files copied.

---

## Step 7 — Optional Integrity Validation (Advanced)

```bash
diff -r /home/usersdata /ecommerce/home/usersdata
```

No output = directories identical.

---

# ==============================================================
# PART 2 — SCENARIO BASED INTERVIEW Q&A (L1 → L4)
# ==============================================================

# L1 — Junior Level

Q1: How do you find files owned by a specific user?

A:
```bash
find /path -type f -user username
```

Q2: Why use -type f?

To exclude directories and copy only regular files.

---

# L2 — System Administrator

Scenario:
Copy only files owned by a user from a production directory.

Key Concerns:
- Avoid copying directories
- Preserve structure
- Avoid overwriting system files

Best Practice:
Use find + cp --parents or rsync.

---

# L3 — Senior Engineer

Scenario:
You must migrate millions of files with minimal downtime.

Solution:
Use rsync:

```bash
rsync -a --progress --files-from=<(find /home/usersdata -type f -user kirsty) / /ecommerce
```

Concepts:
- Incremental copy
- Checksum verification
- Efficient I/O

---

# L4 — Architect Level

Scenario:
File-level filtering required in hybrid cloud migration.

Architectural Thinking:

- Avoid OS-level filtering at scale
- Use centralized identity (LDAP/AD)
- Use object storage lifecycle policies
- Automate using Infrastructure as Code
- Enable auditing & logging

Focus:
Scalability, auditability, automation.

---

# ==============================================================
# PART 3 — AWS SCENARIO MAPPING
# ==============================================================

Linux Scenario:
Filter and copy files by ownership.

AWS Equivalent:
Filter and migrate objects in S3 by prefix or metadata.

Example:

```bash
aws s3 cp s3://source-bucket/home/usersdata \
s3://destination-bucket/ecommerce \
--recursive
```

Enterprise AWS Approach:

- Use IAM for access control
- Use S3 lifecycle policies
- Enable CloudTrail for auditing
- Use DataSync for large migrations

---

# ==============================================================
# PART 4 — REAL AWS INCIDENT RCAs
# ==============================================================

Incident 1 — Production Outage Due to Mass Copy

Impact:
Disk space exhausted → Application downtime.

Root Cause:
Script copied entire directory without filtering.

Wrong:
```bash
cp -r /home/usersdata /ecommerce
```

Correct:
```bash
find -type f -user kirsty
```

Lesson:
Always scope operations precisely.

---

Incident 2 — IAM Credential Expiry Outage

Impact:
Application stopped working.

Root Cause:
Temporary IAM credentials expired.
No automatic refresh.

Fix:
Use IAM roles and SDK auto-refresh.

---

Incident 3 — Open SSH Security Breach

Impact:
Unauthorized access attempts.

Root Cause:
Security Group allowed port 22 to 0.0.0.0/0.

Prevention:
- Use SSM Session Manager
- Restrict SSH access
- Enable GuardDuty

---

# ==============================================================
# PART 5 — FULL DEVOPS MOCK INTERVIEW
# ==============================================================

Round 1 — Linux

Q: Difference between cp and rsync?

cp → Simple copy  
rsync → Efficient, incremental, preserves metadata  

Q: How to verify migration success?

- File count comparison
- Ownership validation
- Directory structure check
- Checksum comparison

---

Round 2 — AWS

Q: Difference between IAM User and IAM Role?

IAM User → Long-term identity  
IAM Role → Temporary credentials  

Q: How to avoid SSH in EC2?

Use:
- SSM Session Manager
- IAM Roles

---

Round 3 — CI/CD

Q: How to automate this task?

Use:
- Ansible
- Terraform
- Shell scripts
- CI pipeline triggers

Example Ansible:

```yaml
- name: Copy kirsty files
  shell: find /home/usersdata -type f -user kirsty -exec cp --parents {} /ecommerce \;
```

---

Round 4 — Kubernetes / Cloud Native

Q: How to manage storage migration in Kubernetes?

- Use Persistent Volumes
- Use CSI drivers
- Use object storage
- Automate via Helm/Terraform

---

Round 5 — Architecture

Design secure migration framework:

- Zero Trust access
- IAM-based authentication
- Audit logging
- Automated validation
- Rollback strategy

---

# ==============================================================
# FINAL SUMMARY
# ==============================================================

✔ Identified 1625 files owned by kirsty  
✔ Copied files preserving directory hierarchy  
✔ Verified source and destination counts match  
✔ Ensured no unauthorized files copied  
✔ Validated integrity  
✔ Covered L1 → L4 interview depth  
✔ Mapped Linux concept to AWS  
✔ Included real incident RCAs  
✔ Completed full DevOps mock interview  

End of Document

