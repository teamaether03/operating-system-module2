# Chapter 7 — Security & Protection

**Core problem:** The OS manages CPU, memory, files, devices, and processes. Without security mechanisms, any user or process could access or modify resources it shouldn't.

> **NOTE : All the commands are written in BASH.**

---

## 1. Authentication

**Problem:** How does the OS know who is requesting access?

**Solution:** Authentication verifies the identity of a user/process

### Examples:

* Password
* SSH key
* Biometrics
* MFA

### Linux experiment:

```bash
whoami
id
w
```

**Check login information:**

```bash
last
```

**Key distinction:** Authentication = Who are you?

---

## 2. Authorization

**Problem:** Knowing who the user is isn't enough. The OS must decide what that user is allowed to do.

**Solution:** Authorization determines whether an authenticated user can access a resource.

### Example:

```text
User: alice
        ↓
Authenticated?
        ↓ YES
Can alice read /etc/shadow?
        ↓
NO → Permission denied
```

**Key distinction:** Authorization = What are you allowed to do?

---

## 3. Permissions

**Problem:** Multiple users/processes share the same system. One user shouldn't automatically be able to modify another user's files.

**Solution:** The OS assigns permissions to resources.

### Linux uses:

```text
r = read
w = write
x = execute
```

### Example:

```bash
ls -l
```

**Output:**

```text
-rwxr-xr--
```

### Breakdown:

```text
Owner   Group   Others
rwx     r-x     r--
```

**Change permissions:**

```bash
chmod 640 file.txt
```

**Change ownership:**

```bash
chown user:group file.txt
```

### Practical experiment

```bash
touch test.txt
chmod 600 test.txt
ls -l test.txt
```

Now only the owner can read/write it.

**Try:**

```bash
chmod 000 test.txt
cat test.txt
```

You should get: Permission denied

**Core idea:** Permissions = resource-level access control

---

## 4. Least Privilege

**Problem:** A process with excessive privileges becomes dangerous if compromised.

### Example:

```text
Web Server
    ↓
Compromised
    ↓
Root privileges
    ↓
Entire system compromised
```

**Solution:** Give a user/process only the privileges required to perform its job.

### Bad IDEA:

```text
Application → root
```

### Better:

```text
Application → restricted user
             ↓
       only required resources
```

### Linux example:

Shows what commands the current user can execute through sudo :

```bash
sudo -l
```

**Check current identity:**

```bash
id
```

**Security principle:** Minimum privilege → minimum damage when compromised.

This is one of the most important principles in OS security.

---

## 5. Sandboxing

**Problem:** Even with permissions, running untrusted software is dangerous.

### Example:

```text
Untrusted Program
       ↓
   Filesystem
       ↓
   Network
       ↓
   Processes
       ↓
   System
```

**Solution:** Put the program inside an isolated environment with restricted access.

```text
+-------------------------+
|       Host OS           |
|                         |
|   +-----------------+   |
|   |    Sandbox      |   |
|   |                 |   |
|   |  Application    |   |
|   |                 |   |
|   | Restricted FS   |   |
|   | Restricted Net  |   |
|   +-----------------+   |
|                         |
+-------------------------+
```

### Linux technologies used for isolation include:

* Namespaces → isolate processes/resources
* cgroups → limit CPU/memory/resources
* seccomp → restrict system calls
* Containers → combine these mechanisms

### Basic experiment if available:

```bash
unshare --fork --pid --mount-proc bash
```

Then:

```bash
ps aux
```

The process view inside the namespace differs from the host.

---

# Security Chain

These concepts work together:

```text
Authentication
      ↓
   WHO are you?
      ↓
Authorization
      ↓
WHAT can you access?
      ↓
Permissions
      ↓
WHAT operations are allowed?
      ↓
Least Privilege
      ↓
HOW MUCH access should you receive?
      ↓
Sandboxing
      ↓
WHAT happens if the program is compromised?
```

When designing any system, ask:

1. Authentication → Who is this?
2. Authorization → What should they access?
3. Permissions → What operations are allowed?
4. Least Privilege → Can we reduce that access further?
5. Sandboxing → What happens if the component gets compromised?

---
