# Linux Security Audit 

## 1. Introduction 

A Linux security audit is a structured process for identifying, examining and documenting potential security weaknesses in a Linux system.

This topic focuses on auditing special file permissions and identifying files or directories that may create security risks. 

## 2. Audit SUID Permissions 

`SUID` (Set User ID) is a special permission bit for executable files. When a `SUID` program runs it generally uses the file owner's effective user ID.

A root owned `SUID` program may therefore execute with elevated privileges, making it important to review these files. 

### Find SUID files 

```bash 
find / -type f -perm -4000 -print 2>/dev/null
```

### Inspect a discovered file

```bash
ls -l /path/to/file
```

Replace `/path/to/file` with the actual file path.

We can recognize `SUID` by looking at the permission:

```bash 
-rwsr-xr-x
```

The `s` in the owners execute position stands for `SUID`.

### What to investigate:

- Is the file expected on the system?
- Who owns it?
- Is the program necessary?
- Can an unauthorized user modify the file or its dependencies?

## 3. Audit SGID Permissions

`SGID` (Set Group ID) is a special permission bit.

Executable files: `SGID` generally causes the program to use the file's group as its effective group.

Directories: `SGID` generally makes newly created files and subdirectories inherit the directory's group.

### Find SGID files

```bash
find / -type f -perm -2000 -print 2>/dev/null
```

### Find SGID directories 

```bash
find / -type d -perm -2000 -print 2>/dev/null
```

We can recognize `SGID` by looking at the permission:

```bash
drwxrwsr-x
```

The `s` in the groups execute position stands for `SGID`.

### What to investigate

- Whether the permission is expected.
- Whether the file or directory has appropriate ownership.
- Whether the group provides more access than necessary.
- Whether the permission is required by the program or shared directory.

## 4. Audit Findings and Verification

For each suspicious permission, record:

- Path: The file or directory examined.
- Permissions: The observed permission bits.
- Owner and group: Who owns the item.
- Finding: Why the configuration may be concerning.
- Action: What investigation or correction is appropriate.
- Verification: How to confirm the result after any change.

Do not remove `SUID` or `SGID` bits or change file permissions simply because a command reports them. Verify the purpose of the item first.

## Summary

This audit focuses on identifying SUID and SGID files and directories, investigating their purpose, and documenting security findings.

These checks help identify items that deserve closer security review. A finding is not automatically a vulnerability. Investigate the item's purpose, ownership, permissions, and potential impact before recommending changes.