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
![WhatsApp Image 2025-12-26 at 07 00 04](https://github.com/user-attachments/assets/73e7f22c-825a-47bc-8eca-41f56e4671cf)

## 4.2 Firewall verification 

Show full ruleset and confirm it only allows SSH from your workstation IP:
```bash
sudo ufw status verbose
sudo ufw status numbered
```
<img width="538" height="381" alt="Screenshot 2025-12-26 070232" src="https://github.com/user-attachments/assets/9bf2a868-2253-44c1-8343-241896e1afe6" />

# 5. Access Control Verification (SELinux/AppArmor)
## 5.1 Confirm Status

AppArmor
```bash
sudo aa-status
sudo apparmor_status
```
<img width="439" height="778" alt="Screenshot 2025-12-26 070431" src="https://github.com/user-attachments/assets/5b2903bf-8b2f-4700-b70f-4ee30b6e4310" />

# 6. Service Inventory + Justification
## 6.1 List running services
```bash
systemctl --type=service --state=running
```
<img width="935" height="445" alt="Screenshot 2025-12-26 070613" src="https://github.com/user-attachments/assets/ee98bb67-496b-4aa8-a8e4-ded529652e81" />

## 6.2 List listening ports + owning process
```bash
sudo ss -tulpn
```
<img width="1258" height="339" alt="Screenshot 2025-12-26 070751" src="https://github.com/user-attachments/assets/a72c17a2-3101-4503-bfc4-5810c1892c9f" />

| Service               | Status          | Purpose / Why it’s Needed        | Risk if Exposed                 | Mitigation Measures                                  |
|-----------------------|-----------------|----------------------------------|----------------------------------|------------------------------------------------------|
| SSH                   | Running         | Remote server administration     | Brute-force attacks, key theft   | SSH key-only login, Fail2Ban, firewall allowlist      |
| Unattended-Upgrades   | Running (Timer) | Automatic security patching      | Low                              | Verified configuration, update logs                  |
| Fail2Ban              | Running         | Blocks repeated login attempts   | Low                              | Log monitoring, tuned ban rules                      |

# 7. System Configuration Review (Security Baseline Check)
```bash
./security-baseline.sh <server_ip> <admin_user>
```
<img width="1130" height="661" alt="Screenshot 2025-12-26 072324" src="https://github.com/user-attachments/assets/eae94cc6-cf3c-4c4f-8aad-56b93620e1fd" />

# 8. Network Security Testing Result

## Basic Port Scan
```bash
nmap -Pn 192.168.56.101
```
<img width="698" height="198" alt="Screenshot 2025-12-26 074438" src="https://github.com/user-attachments/assets/2e92a69b-6ebc-4902-9e3b-9e1e74fad18d" />

## Service & Version Detection
```bash
nmap -Pn -sV 192.168.56.101
```
<img width="961" height="247" alt="Screenshot 2025-12-26 074548" src="https://github.com/user-attachments/assets/58aa7c01-f3ca-4bf9-bfea-35a71f8cfa2e" />

## Script Scan
```bash
nmap -Pn -sC 192.168.56.101
```

## Verification Against Local Services
```bash
sudo ss -tulpn
```
<img width="1213" height="485" alt="Screenshot 2025-12-26 075243" src="https://github.com/user-attachments/assets/132eade5-8665-408d-8b7b-64722362c548" />


# 9. Week 7 Reflection (What I Learned)

This week showed me that a default Linux installation is functional but not securely hardened by default. Running Lynis highlighted insecure assumptions such as unnecessary services being enabled, weak default configurations, and missing hardening settings that are not obvious during normal use. Seeing the Lynis hardening score and warnings made security issues concrete rather than theoretical. After reviewing real evidence, I disabled unused services, verified firewall rules, and confirmed that only SSH was exposed to the network. I also validated that SSH was hardened using key-based authentication and that access controls were correctly enforced. These changes directly improved my system’s attack surface and audit results. However, I also learned that stronger security often reduces convenience. For example, restricting SSH access by IP improves security but makes administration harder if the client IP changes. Overall, this phase reinforced that security is a balance between protection, usability, and maintainability rather than a one-time configuration.
