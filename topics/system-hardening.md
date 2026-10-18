# System Hardening

## 1. Introduction 

System hardening is the process of reducing security risks by applying secure configurations, installing security updates, limiting unnecessary services, and protecting important system files.

The goal is to reduce the attack surface and make the system secure.

## 2. Security Update and Package Management 

### Commands:

### Refresh Package Information:

```bash
sudo apt update 
```

### List packages with available upgrades:

```bash
apt list --upgradable
```

### Inspect configured APT repository sources:

```bash
cat /etc/apt/sources.list
```

### Some systems also store repository definitions in:

```bash
/etc/apt/sources.list.d/
```

### What to check :

- Whether security updates are available.
- Whether repositories come from trusted sources.
- Whether the system has a reliable update process.
- Whether updates have been delayed unnecessarily.

Not every available upgrade is a security update. Review package information and repository origins before drawing conclusions.

## 3. Review Security Configuration and Software

System security depends on configuration settings as well as installed software.

Inspect important system configuration files and compare their settings with the requirements of the system.

For example
- Check the permissions and ownership of a sensitive configuration file:

```bash
stat /etc/ssh/sshd_config
```

- Check the effective SSH server configuration:

```bash 
sudo sshd -T
```

- Security Point: We must record unexpected permissions or configuration settings and investigate them before making changes.

## 4. SELinux Security

SELinux Security provides extra access control on top of normal Linux permissions. It can restrict what processes are allowed to access, even when traditional Linux permissions might otherwise allow access.

### Check SELinux status 

```bash
getenforce
```

### Display detailed status, if available:

```bash
sestatus
```

Common modes:

- Enforcing : Blocks unauthorized actions.
- Permissive : Allows actions but logs violations.
- Disabled : SELinux is turned off.

Change the mode:

`sudo setenforce 0` - Changes to permissive
`sudo setenforce 1` - Changes to enforcing

Remember that setenforce changes the mode temporarily.

### Hardening principles

- Prefer enforcing mode for systems that rely on SELinux protection.
- Investigate denials before changing policies.
- Avoid disabling SELinux merely to make an application work.
- Correct file contexts and policies when evidence shows they are wrong.
- Verify the effect of any policy changes.

## 5. Automatic Update Checks

On Debian systems using APT, periodic package activities can be inspected with:

```bash
systemctl status apt-daily.timer

systemctl status apt-daily-upgrade.timer
```

## 6. System Hardening Audit

1. Review available upgrades:

`apt list --upgradable`

2. Inspect running services:

`systemctl list-units --type=service --state=running`

3. Inspect listening sockets:

`sudo ss -lntup`

4. Check update timers:

`systemctl status apt-daily.timer`

`systemctl status apt-daily-upgrade.timer`

5. Check SELinux status, if installed:

`getenforce`

`sestatus`

Record the results and investigate unexpected findings. 

### Summary:

System hardening reduces security risks through trusted updates, minimal service exposure, secure configurations, and mandatory access controls.

Effective hardening requires evidence-based changes followed by verification. Avoid disabling protections or changing system settings without understanding the consequences.