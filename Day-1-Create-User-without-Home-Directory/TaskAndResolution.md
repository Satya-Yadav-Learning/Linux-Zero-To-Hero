# TaskandResolution.md

## Task

Create a service user named `mark` on App Server 3 without creating a home directory

## Resolution

### Step 1: Create User Without Home Directory

```bash
useradd -M mark
```

### Command Explanation

- useradd → Creates a new user account.
- -M → Prevents creation of the home directory.
- mark → Name of the user being created.

---

### Step 2: Verify User Creation

```bash
id mark
```

### Output

```bash
uid=1002(mark) gid=1002(mark) groups=1002(mark)
```

### Explanation

- id → Displays user ID (UID), group ID (GID), and group memberships.
- Confirms user `mark` exists in the system.

---

### Step 3: Confirm Home Directory Is Not Created

```bash
ls -ld /home/mark
```

### Output

```bash
ls: cannot access '/home/mark': No such file or directory
```

### Explanation

- ls -ld → Lists directory details.
- Output confirms `/home/mark` directory does not exist.
- Validates that `-M` option worked correctly.

---

### Step 4: Verify Entry in /etc/passwd

```bash
grep mark /etc/passwd
```

### Output

```bash
mark:x:1002:1002::/home/mark:/bin/bash
```

### Explanation of Fields

username : password_placeholder : UID : GID : comment : home_directory : shell

- mark → Username
- x → Password stored in /etc/shadow
- 1002 → UID
- 1002 → GID
- /home/mark → Default home directory path (not physically created)
- /bin/bash → Default login shell

---

## Final Validation

✔ User `mark` created successfully  
✔ No home directory exists  
✔ Entry present in `/etc/passwd`  
✔ Requirement fulfilled

