# Network and Service Security

## 1. Introduction 

Network and service security reduces unauthorized access by identifying exposed services, reviewing firewall rules, and limiting unnecessary network access.

This topic focuses on practical security auditing, risk assessment, and verification.

## 2. Audit Listening Ports

Listening ports show where programs are waiting for network connections.

**Commands** 

- To display `TCP` `UDP` sockets with process information: 
`sudo ss -lntup`

- To display all `TCP` connections and listening sockets:
`sudo ss -antp`

- To display listening `TCP ports` only : 
`sudo ss -lnt` 

- To display listening `UDP ports` only: 
`sudo ss -lnu` 

**What to look for:**

- Unexpected listening ports.
- Services listening on all network interfaces.
- Services running under unexpected accounts.
- Ports associated with services that are not required.

A listening port alone does not prove that a service is reachable from another computer or that it is vulnerable.

## 3. Identify the Service Behind a Port

- Check a specific service:
`systemctl status ssh` 

- List running services:
`systemctl list-units --type=service --state=running`

- Inspect details of a specific service:
`systemctl cat ssh` 

Replace `ssh` with the service name you are investigating.

**Audit procedure:**

- Identify the listening port.
- Determine which process owns it.
- Identify the related service.
- Establish why it is running.
- Decide whether its network exposure is necessary.

Do not disable unfamiliar services without investigating their purpose.

## 4. Review Firewall Rules

`sudo ufw status` 

A firewall controls which network traffic is allowed or blocked.

`UFW` (Uncomplicated Firewall) - It is a tool for managing firewall rules. 

**UFW Commands** 

- Check firewall status and rules:
`sudo ufw status verbose` 

- Display numbered rules: 
`sudo ufw status numbered` 

- To remove the verified rule: 
`sudo ufw delete RULE_NUMBER`

Replace the `RULE_NUMBER` with the actual number shown by `ufw`.

- Check the `UFW` application profiles available on the system:
`sudo ufw app list` 

- Inspect the details of an available profile:
`sudo ufw app info "OpenSSH"`

**What to look for:** 

- Whether the firewall is active.
- Whether incoming connections are allowed unnecessarily.
- Whether rules permit access from overly broad networks.
- Whether IPv6 traffic is handled as intended.
- Whether permitted services are genuinely required.

Security note: An inactive UFW firewall does not automatically mean the system has no firewall protection. Other firewall tools or network-level controls may be in place.

## 5. Assess Network Exposure

- Review the host's network addresses:
`ip -br address` 

- Review the routing table: 
`ip route` 

These commands help identify interfaces and routes relevant to the exposure assessment.

## 6. Review Unnecessary Services

Running services may introduce additional attack surface, especially when they accept network connections.

- Identify running services:
`systemctl list-units --type=service --state=running` 

- Inspect a service before changing it:
`systemctl status SERVICE_NAME`

- Review its unit configuration:
`systemctl cat SERVICE_NAME`

- Check whether it is configured to start automatically:
`systemctl is-enabled SERVICE_NAME`

Replace `SERVICE_NAME` with the actual service unit, such as `cups.service`.

**Safe remediation workflow**

First, confirm that the service is not required and investigate its dependencies. In a test environment, you can stop a service temporarily by:
`sudo systemctl stop SERVICE_NAME`

- Recheck listening sockets:
`sudo ss -lntup`

- If the service is confirmed unnecessary, consider disabling automatic startup:
`sudo systemctl disable SERVICE_NAME`

## 7. Investigate Service Logs

Logs can help explain unexpected service's and connection problems.

- For `SSH` on Debian:
`sudo journalctl -u ssh --since "1 hour ago"`

- For another service:
`sudo journalctl -u SERVICE_NAME --since today`

- Follow new log entries for a service:
`sudo journalctl -u SERVICE_NAME -f`

Log availability and detail depend on the operating system and logging configuration.

## 8. Practical Security Audit Workflow

1. Inspect listening sockets
`sudo ss -lntup`

2. Review network interfaces
`ip -br address`

3. Review routes
`ip route`

4. Inspect firewall configuration
`sudo ufw status verbose`

5. List running services
`systemctl list-units --type=service --state=running`

6. Inspect `SSH` service status
`systemctl status ssh`

7. Review recent SSH logs
`sudo journalctl -u ssh --since "1 hour ago"`

One safety reminder: don't stop or disable a service until you've confirmed it's unnecessary and checked its dependencies.

## Summary

Network and service security involves identifying exposed services, assessing network reachability, reviewing firewall controls, investigating unnecessary services, and verifying corrective actions.

A useful security audit combines commands, evidence, risk assessment, safe remediation, and verification.