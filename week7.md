# Week 7
Overview

In Week 7, a full security audit was conducted on the Linux server to evaluate the effectiveness of the implemented security controls and to identify remaining risks. This phase consolidates work from previous weeks by validating SSH hardening, firewall rules, access control mechanisms, and service exposure. Industry-standard security auditing tools were used, including Lynis and nmap, to assess system posture and resilience against common threats.

# 1. Infrastructure Security Assessment

The server is deployed as a headless Linux server, administered exclusively via SSH from a trusted workstation. The following baseline controls are in place:

- SSH key-based authentication (password login disabled)
- Firewall restricting SSH access to a single workstation IP
- Non-root administrative user with sudo privileges
- Automatic security updates enabled
- AppArmor mandatory access control enforced
- Fail2ban intrusion prevention enabled
  
This audit verifies that these controls are active and correctly configured.
