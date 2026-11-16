# linux-security-baseline
A practical, documented Linux hardening checklist and configuration baseline for Debian/Ubuntu servers, focused on SSH, firewall, and access control.
# Linux Security Baseline

## Purpose
This repository documents my practical approach to hardening Linux servers. It serves as both a living checklist for my daily work and a demonstration of defensive security practices.

## Environment
*   **OS:** Ubuntu Server 22.04
*   **Focus:** SSH Hardening, Host Firewall (nftables/ufw), Intrusion Prevention (Fail2ban), Access Control.

## Baseline Checklist (Work in Progress)
- [ ] **SSH:** Disable password authentication (key-only), disable root login, change default port.
- [ ] **Firewall:** Default deny incoming, allow only necessary ports (e.g., SSH, HTTP/HTTPS).
- [ ] **Access Control:** Review sudoers file, restrict sudo access to specific users.
- [ ] **Intrusion Prevention:** Install and configure `fail2ban` for SSH.
- [ ] **Auditing:** Check permissions on sensitive files (`/etc/shadow`, `/etc/passwd`).

*(I will update this checklist as I test and document each configuration in the `/docs` folder).*
