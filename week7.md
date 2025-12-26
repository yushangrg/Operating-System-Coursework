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

# 2. Lynis Security Audit
## 2.1 Lynis Installation

Lynis was installed and executed via SSH using the following commands:
```bash
sudo apt update
sudo apt install lynis -y
```

## 2.2 Initial Lynis Scan
```bash
sudo lynis audit system
```
<img width="741" height="764" alt="Screenshot 2025-12-26 025656" src="https://github.com/user-attachments/assets/a84e871b-718c-43b1-8395-92f6d557b275" />

## 2.3 Lynis Score (Before Remediation)

Initial Hardening Index: XX

Several warnings were identified, including:
- Insecure SSH configuration suggestions
- Missing firewall-related hardening checks
- Kernel parameter recommendations

## 2.4 Remediation Actions

The following actions were taken based on Lynis recommendations:

- Verified SSH root login disable
- Confirmed UFW firewall active and restrictive
- Ensured automatic security updates enabled
- Reviewed AppArmor enforcement mode
- Verified fail2ban service status

Example verification commands:
```bash
sudo ufw status verbose
sudo systemctl status fail2ban
sudo aa-status
```

### 2.5 Lynis Scan After Remediation
```bash
sudo lynis audit system
```

<img width="976" height="802" alt="Screenshot 2025-12-26 032655" src="https://github.com/user-attachments/assets/0e0192a0-a9e2-47dd-a612-42bd39d04326" />

# 3. Network Security Assessment (nmap)

Network exposure was assessed from the workstation system using nmap.

# Install and run
```bash
sudo apt update
sudo apt install -y nmap
```
<img width="807" height="425" alt="Screenshot 2025-12-26 065801" src="https://github.com/user-attachments/assets/a939f94c-69b6-40d2-9941-876cb9b4bd94" />

### 3.1 Port Scan Command
```bash
sudo nmap -sS -Pn 192.168.56.101
```
<img width="677" height="225" alt="Screenshot 2025-12-26 035123" src="https://github.com/user-attachments/assets/10400d0c-0f10-4d33-af0d-1b5a468c464d" />

# 4. SSH Security Verification

SSH configuration was reviewed to ensure compliance with best practices.

## 4.1 Configuration Checks
```bash
sudo sshd -T | grep -E "passwordauthentication|permitrootlogin"
```


