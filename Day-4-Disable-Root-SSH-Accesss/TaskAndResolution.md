
# ==============================================================
# TASK
# ==============================================================

Disable direct SSH root login on all application servers

Objective:
- Prevent direct SSH login as root
- Maintain sudo-based administrative access
- Ensure SSH service remains operational
- Validate configuration effectively

---

# ==============================================================
# PART 1 — IMPLEMENTATION & RESOLUTION
# ==============================================================

## Step 1 — Connect to App Server

From jumphost:

```bash
ssh user@server
```

Switch to root:

```bash
sudo -i
```

---

## Step 2 — Verify Current Root Login Status

Check effective SSH configuration:

```bash
sshd -T | grep permitrootlogin
```

### Example Output (Before Fix)

```
permitrootlogin yes
```

Meaning:
- Root login is currently allowed
- Security risk present

---

## Step 3 — Edit SSH Configuration

Open SSH config:

```bash
vi /etc/ssh/sshd_config
```

Modify or add:

```
PermitRootLogin no
```

Save file.

---

## Step 4 — Verify Effective Configuration

```bash
sshd -T | grep permitrootlogin
```

### Output After Change

```
permitrootlogin no
```

This confirms:
- SSH daemon now disallows root login

---

## Step 5 — Restart SSH Service Safely

```bash
systemctl restart sshd
```

Verify service status:

```bash
systemctl status sshd
```

### Example Output

```
Active: active (running)
```

---

## Step 6 — Validate Root Login Is Blocked

From jumphost:

```bash
ssh user@server

sudo -i
```

### Expected Output

```
Permission denied
```

✔ Root login successfully disabled

# ==============================================================
# IMPORTANT TROUBLESHOOTING INSIGHT
# ==============================================================

During implementation, observed:

```
permitrootlogin yes
```

Even after editing configuration.

Root Cause:
- Containerized environment restarted sshd
- Default configuration reapplied

Resolution:
- Re-applied configuration
- Verified using `sshd -T`
- Restarted sshd correctly
- Validated externally from jumphost

---

# ==============================================================
# FINAL VALIDATION CHECKLIST
# ==============================================================

For each server:

```bash
sshd -T | grep permitrootlogin
```

Must return:

```
permitrootlogin no
```

External test:

```bash
ssh root@<server_ip>
```

Must return:

```
Permission denied
```

---

# ==============================================================
# PART 2 — SCENARIO BASED INTERVIEW Q&A (L1 → L4)
# ==============================================================

# L1 — Junior Level

Q: Why disable root SSH login?

A:
To reduce attack surface and prevent brute-force attacks.

Q: How do you disable it?

A:
Set `PermitRootLogin no` in sshd_config and restart sshd.

---

# L2 — System Administrator

Scenario:
After disabling root login, users cannot access server.

Root Cause:
No sudo user configured.

Best Practice:
- Ensure sudo user exists before disabling root
- Keep one session active during testing

---

# L3 — Senior Engineer

Scenario:
Root login still works after config change.

Investigation:
- Use `sshd -T` to check effective config
- Check override files in sshd_config.d
- Verify correct daemon restarted

Key Insight:
Configuration ≠ Effective runtime state

---

# L4 — Architect Level

Enterprise Hardening Strategy:

- Disable root SSH login
- Disable password authentication
- Enforce SSH key-based login
- Enable MFA
- Implement centralized identity (LDAP/AD)
- Log SSH sessions
- Use bastion host

Security Principle:
Least Privilege + Defense in Depth

---

# ==============================================================
# PART 3 — AWS SCENARIO MAPPING
# ==============================================================

Linux Hardening:
Disable root SSH login

AWS Equivalent:

- Disable root account access
- Use IAM roles instead of root credentials
- Use SSM Session Manager instead of SSH

---

## AWS Scenario 1 — EC2 Hardening

Instead of SSH root:

Use:
- IAM role attached to EC2
- SSM Session Manager
- No inbound port 22

---

## AWS Scenario 2 — Real Incident RCA

Incident:
Production EC2 compromised.

Root Cause:
Port 22 open to 0.0.0.0/0
Root login enabled.

Impact:
Unauthorized access attempt.

Resolution:
- Disabled root SSH
- Restricted security groups
- Enabled GuardDuty
- Enforced IAM role access

Lesson:
Never allow direct root SSH access in production.

---

## AWS Scenario 3 — IAM Misuse

Incident:
Root credentials used for automation.

Impact:
Audit failure.

Fix:
- Disable root API access
- Use IAM roles
- Enable CloudTrail monitoring

---

# ==============================================================
# PART 4 — REAL INCIDENT RCAs
# ==============================================================

Incident 1 — Brute Force Attack

Cause:
Root SSH enabled.

Impact:
High CPU due to authentication attempts.

Resolution:
- Disable root login
- Enable Fail2Ban
- Limit MaxAuthTries

---

Incident 2 — Accidental System Deletion

Cause:
Shared root account used.

Impact:
Accountability lost.

Resolution:
- Enforce sudo-based access
- Log commands
- Individual user accounts

---

# ==============================================================
# PART 5 — FULL DEVOPS MOCK INTERVIEW
# ==============================================================

Round 1 — Linux

Q: Difference between sshd_config and sshd -T?

A:
- sshd_config = configuration file
- sshd -T = effective runtime configuration

---

Round 2 — Security

Q: Why is root login dangerous?

A:
- No accountability
- High privilege
- Common attack target

---

Round 3 — Cloud

Q: How do you secure EC2 SSH access?

A:
- Use IAM role
- Disable root login
- Restrict Security Groups
- Prefer SSM over SSH

---

Round 4 — Architecture

Design secure access model:

- Bastion host
- Key-based SSH only
- MFA enforced
- Centralized logging
- Session recording
- RBAC policies

---

# ==============================================================
# FINAL SUMMARY
# ==============================================================

✔ Root SSH login disabled on all app servers  
✔ Verified via sshd -T  
✔ Restarted SSH safely  
✔ External validation completed  
✔ Followed security best practices  
✔ L1 → L4 interview depth covered  
✔ AWS mapping included  
✔ Real incident RCAs documented  
✔ Full DevOps mock interview included  

---

END OF DOCUMENT

