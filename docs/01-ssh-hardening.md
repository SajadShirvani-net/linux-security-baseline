# Week 1: SSH Hardening Baseline

## Objective
The objective of this lab is to secure the SSH service on an Ubuntu 22.04 server by moving away from default configurations. This reduces the attack surface by mitigating automated brute-force attacks, preventing direct root access, and enforcing cryptographic key-based authentication over weak passwords.

## Baseline (Before Hardening)
Prior to making any changes, the SSH server was audited to establish a baseline. 

Command run:
`sudo sshd -T | grep -E "permitrootlogin|passwordauthentication|port"`

Output:
```text
port 22
permitrootlogin without-password
passwordauthentication yes
```
## Actions Taken
### 1. Changed Default SSH Port
Automated scanners primarily target Port 22. Changing the port reduces log noise and stops basic automated attacks.

    Modified /etc/ssh/sshd_config: Changed #Port 22 to Port 2222.

    Added UFW rule to allow the new port: sudo ufw allow 2222/tcp.

### 2. Disabled Root Login

While Ubuntu defaults to without-password for root, explicitly setting it to no ensures that even if a root SSH key is compromised, direct root login over SSH is impossible.

    Modified /etc/ssh/sshd_config: Changed #PermitRootLogin prohibit-password to PermitRootLogin no.

### 3. Enforced Key-Based Authentication

Passwords are susceptible to brute-forcing and credential stuffing. SSH keys provide much stronger cryptographic authentication.

    Generated an ED25519 keypair on the local Windows host: ssh-keygen -t ed25519.

    Copied the public key to the server's ~/.ssh/authorized_keys file.

    Modified /etc/ssh/sshd_config: Changed PasswordAuthentication yes to PasswordAuthentication no. Changed KbdInteractiveAuthentication to no.

**Troubleshooting Note:** After disabling passwords, the server still prompted for a password. I discovered that Ubuntu's `cloud-init` package drops a configuration file at `/etc/ssh/sshd_config.d/50-cloud-init.conf` containing `PasswordAuthentication yes`. Because files in the `sshd_config.d/` directory are processed after the main config, it overrode my hardening. I resolved this by deleting the file: `sudo rm /etc/ssh/sshd_config.d/50-cloud-init.conf`.

### Verification
* Before restarting the service, the configuration syntax was validated using sudo sshd -t.

* The service was restarted using sudo systemctl restart ssh.

* Attempting to SSH as root on port 2222 resulted in immediate rejection.

* Attempting to SSH as user using a password failed (as expected).

* Attempting to SSH as user using the generated ED25519 key successfully logged in without prompting for a password.

* Verified password rejection by forcing the client to use password auth: `ssh -o PreferredAuthentications=password -p 2222 sajad@192.168.133.128`. The server correctly returned `Permission denied (publickey).`
