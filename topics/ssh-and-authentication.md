# SSH and Authentication Security

## 1. Purpose 

`SSH` (Secure Shell) provides encrypted remote access to computers over a network.

From a security perspective, the goal is to ensure that:

- Only authorized users can connect.
- Authentication credentials remain protected.
- Private keys are not exposed.
- Unauthorized access attempts can be detected.
- `SSH` configuration does not create unnecessary security risks.

Default `SSH` port: `TCP 22`.

## 2. SSH Client and Server

- `SSH` client: Initiates a connection to another computer.
- `SSH` server: Accepts incoming SSH connections.
- `OpenSSH`: A widely used implementation of SSH.

On Debian, to check the client version run: 
`ssh -V`

To check the SSH server service:
`systemctl status ssh` 

## 3. SSH Authentication Methods

`SSH` uses two authentication methods.

- Password authentication

The user authenticates using an account password.

Security consideration: Passwords may be exposed through guessing, force attempts, or compromised credentials.

- Public-key authentication

The client proves possession of a private key corresponding to a public key authorized by the server.

Security advantage: Properly configured key-based authentication can reduce dependence on reusable account passwords.

## 4. Public and Private SSH Keys

**Private key** - Remains on the client and must be protected.

`id_ed25519` is the private key

**Public key** - Can be installed on the server to authorize the corresponding private key.

`id_ed25519.pub` is the public key.

A common `Ed25519` key pair uses these filenames:

`~/.ssh/id_ed25519`
`~/.ssh/id_ed25519.pub`

**Security precautions** :

- Never share or upload your private key.
- Protect it with a strong passphrase when appropriate.
- Do not overwrite an existing key without checking whether it is still needed.
- A key passphrase protects the private key file; it is different from your Linux account password.

## 5. Authorized Files 

The server uses the following file to identify public keys authorized for a particular account:
`~/.ssh/authorized_keys`

The authentication process works as follows:

- Generate a key pair on the client.
- Place the public key in the server user's `authorized_keys` file.
- Keep the private key on the client.
- Connect to the server and authenticate using the corresponding private key.

Important point: authorized_keys contains public keys, never private keys. Only authorize keys belonging to trusted users or devices.

## 6. Protect SSH Key Files

`SSH` may reject keys if their permissions are to permissive 

| Path | Permission | Purpose | 
| --- | --- | --- | 
| `~/.ssh/` | 700 | Restricts access to the directory owner. | 
| `id_ed25519` | 600 | Protects the private key. | 
| `id_ed25519.pub` | 644 |  Allows the public key to be read. |
| `authorized_keys` | 600 | Restricts modification of authorized keys. |

## 7. Secure SSH Server Configuration

- The main OpenSSH configuration file on Debian is :

`/etc/ssh/sshd_config`

**Important Settings** : 

| Settings | Security Purpose | 
| --- | --- | 
| `PermitRootLogin` | Controls direct `SSH` login as root. |
| `PasswordAuthentication` | Controls password based `SSH` authentication. | 
| `PubkeyAuthentication` | Controls Public key authentication. | 
| `AllowUsers` | Can restrict `SSH` access to specified accounts. | 

- Inspect the effective configuration:
`sudo sshd -T` 

- Check configuration syntax:
`sudo sshd -t` 

**Hardening principles** :

- Disable direct root login when appropriate.
- Consider disabling password authentication after confirming key-based access works.
- Restrict access to necessary accounts.
- Keep the `SSH` server updated.
- Expose `SSH` only to networks that require access, where practical.

## 8. Monitor SSH Authentication Events

- On Debian systems using the systemd journal, SSH-related logs can be reviewed with:
`sudo journalctl -u ssh` 

- Review recent SSH events:
`sudo journalctl -u ssh --since "1 hour ago"`

## SSH Security Review 

- Only trusted public keys are authorized.
- Private keys are protected and never shared.
- SSH access is limited to accounts that need it.
- The SSH service is not exposed unnecessarily.
- Authentication events are reviewed when investigating suspicious activity.
- Configuration changes are validated before being applied.
- A recovery method is available before changing remote-access settings.

## Summary

- `SSH` security focuses on strong authentication, protected private keys, controlled account access, secure server configuration, and monitoring authentication activity.

- This file covers SSH-specific security practices. General Linux commands, basic permissions, service management, and logging fundamentals are documented separately in the Linux Fundamentals repository.