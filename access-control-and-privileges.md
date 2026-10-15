# Linux Access Control and Privileges

## 1. Overview

Access control ensures that only authorized users and processes can access files, execute commands, and perform administrative tasks.

This document focuses on security auditing and privilege risks. Basic `chmod`, `chown`, and `rwx` permissions are covered in the Linux Fundamentals Lab.

## 2. Audit Users and Groups

Unexpected accounts or excessive administrative privileges can increase security risks.

Useful commands:

```bash
who
w
id
getent passwd
getent group sudo
sudo -l
```

Check for:
- Unknown or unexpected user accounts.
- Users with unnecessary sudo privileges.
- Unexpected login sessions or group memberships.


## 3. Least Privilege and sudo

**Principle of least privilege:** Give each user or process only the permissions needed for its task.

Security best practices:
- Use a regular account for everyday activities.
- Use `sudo` only when necessary.
- Avoid unrestricted administrative access when narrower permissions are sufficient.
- Never run unknown scripts as root.
- Review administrative access periodically.

Check the sudo configuration:

```bash
sudo -l
sudo visudo -c
```

Use `visudo` when editing sudo rules because it checks the configuration for syntax errors.

## 4. Review Sensitive Files

Inspect permissions on important system files:

```bash
ls -l /etc/passwd /etc/shadow /etc/sudoers
stat /etc/shadow
```

On a typical Debian system:
- `/etc/passwd` is generally readable by users.
- `/etc/shadow` contains password-hash information and should be restricted.
- `/etc/sudoers` defines sudo access and must be protected against unauthorized modification.

Do not change system-file permissions without understanding the consequences.

## 5. Safe Practice Lab

Create a test file with unnecessarily broad permissions:

```bash
mkdir -p ~/security-lab/access-control
cd ~/security-lab/access-control
touch audit-test.txt
chmod 666 audit-test.txt
ls -l audit-test.txt
```

Correct the permissions:

```bash
chmod 600 audit-test.txt
ls -l audit-test.txt
```

The final permissions should be `-rw-------`, allowing only the owner to read and write the file.

This exercise affects only your practice file.

## 6. Remediation and Verification

When you identify a potential security issue:

1. **Record** the finding and its current configuration.
2. **Verify** whether it is genuinely a security problem.
3. **Plan** the minimum necessary correction.
4. **Remediate** only when authorized.
5. **Verify again** that the issue is resolved and the system still works.

## Summary

Access-control auditing focuses on unnecessary privileges, sensitive file permissions, and excessive sudo access.

**Safety rule:** Audit first, investigate findings, and make only necessary changes.
