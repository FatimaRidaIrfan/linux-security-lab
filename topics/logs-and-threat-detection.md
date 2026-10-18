# Logs and Threat Detection

## 1. Introduction

System logs record events that occur on a Linux system.

Logs and threat detection involve examining these records to identify suspicious activity, authentication failures, unexpected service behavior, and other events that may require investigation.

## 2. Common Linux Security Logs

Linux systems may store logs in `/var/log/` or in the `systemd` journal.

Common log files include:

| Log file | Purpose |
| --- | --- | 
| /var/log/auth.log | Authentication and authorization events on Debian-based systems. |
| /var/log/syslog | General system messages on systems using rsyslog. |
| /var/log/audit/audit.log | Audit events when Linux Audit is installed and configured. |

Log files vary by distribution and configuration.

## 3. Investigating Authentication Events

Authentication logs can help identify failed login attempts, successful logins, and other account-related activity.

### View SSH service logs

```bash
sudo journalctl -u ssh --since "24 hours ago"
```

This displays available journal entries for the SSH service from the last 24 hours.

### Search SSH logs for failed attempts

```bash
sudo journalctl -u ssh --since "24 hours ago" | grep -i "failed"
```

This searches matching SSH journal entries for the word failed, ignoring capitalization.

### Search the Debian authentication log

```bash 
sudo grep -i "failed" /var/log/auth.log
```

This command works when `/var/log/auth.log` exists and contains the relevant records.

### Look for:

- Repeated failed login attempts.
- Login attempts involving unfamiliar accounts.
- Unexpected successful logins.
- Activity at unusual times.
- Repeated attempts against multiple accounts.

A failed login alone does not prove suspicious activity. Consider the source, timing, frequency, and surrounding events.

## 4. Monitoring Logs in Real Time

Real-time monitoring allows administrators to observe new events while a service is operating.

### Follow SSH service logs

```bash
sudo journalctl -u ssh -f
```

### Follow a log file, if available

```bash 
sudo tail -f /var/log/auth.log
```

The second command requires the authentication log file to exist and be readable.

### Observe :

- New authentication failures.
- Unexpected service errors.
- Repeated messages within a short period.
- Activity that differs from normal system behavior.

Real-time monitoring shows events as they are recorded, but it does not automatically determine whether they are suspicious.

## 5. Log Investigation Workflow

A consistent process when investigating a possible security event.

1. Identify the event.

Determine what appears unusual, such as repeated failed `SSH` logins or an unexpected service error.

2. Select the relevant log source.

For `SSH` activity, start with the `SSH` service journal or the authentication log, if available.

3. Narrow the time range.

Use options such as `--since` to focus on the period when the event occurred.

4. Search for related evidence.

Look for other messages involving the same account, service or time period.

5. Assess the findings 

Consider whether the activity is expected, whether it repeats, and whether additional evidence supports a security concern.

6. Document the result 

Record the event, evidence, possible explanation, and any follow-up action required. 

Do not delete logs or make disruptive system changes simply because an event appears suspicious.

## Summary 

Logs and threat detection help us investigate suspicious authentication activity, service error and unusual system events.

Useful tools include `journalctl`, `grep`, and `tail`. Logs provide evidence, but they must be interpreted in context before drawing security conclusions.