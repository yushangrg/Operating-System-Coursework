## Week 4
Overview

This week focuses on deploying the Linux server and implementing foundational security controls. All administrative tasks were performed remotely via SSH from the workstation system, in line with the coursework constraints. The objective was to harden remote access, restrict network exposure, and enforce proper privilege management.

# 1. SSH Key-Based Authentication Configuration
Objective: Replace password authentication with key-based authentication for enhanced security.

## 1. Generated SSH key pair on workstation:
```bash
ssh-keygen -t rsa -b 4096 -C "A00022006@yushan-VirtualBox"
```
<img width="877" height="550" alt="Screenshot 2025-12-25 213425" src="https://github.com/user-attachments/assets/23ff9181-36b0-4959-8f43-bdfff1833ca8" />

## Create user and add to sudo group
<img width="874" height="76" alt="Screenshot 2025-12-26 053940" src="https://github.com/user-attachments/assets/8c5252a5-fce3-4df5-95fb-2e27f574feed" />

## Verify sudo works
<img width="709" height="186" alt="image" src="https://github.com/user-attachments/assets/8bcc07ef-a570-4115-997c-ba3b09b3cc50" />


## 2.Copied public key to server:
```bash
ssh-copy-id student@192.168.x.x
```
## 3.Modified SSH configuration on server:
```bash
sudo nano /etc/ssh/sshd_config
```
## Changed settings:
![WhatsApp Image 2025-12-26 at 05 55 57](https://github.com/user-attachments/assets/776ab0b8-3596-4552-a1a6-f8e6c02bbbe4)

- PasswordAuthentication no
- PubkeyAuthentication yes
- PermitRootLogin no

## 5. Restarted SSH service:
```bash
sudo systemctl restart sshd
```
## 2. Firewall Configuration (UFW)
Objective: Configure firewall to permit SSH access only from the specific workstation IP.

## Enabled UFW:
```bash
sudo ufw enable
```
<img width="940" height="387" alt="image" src="https://github.com/user-attachments/assets/eaf0c20a-e6c8-4b9c-a8af-9866ccaee446" />

## Set default policies:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
## Added rule for workstation IP only:
```bash
sudo ufw allow from 192.168.x.x to any port 22
```
## Verified rules:
```bash
sudo ufw status verbose
```
# 3. User Management and Privilege Configuration
Objective: Create non-root administrative user with sudo privileges.

## Created new user:
```bash
sudo adduser adminuser
```
<img width="940" height="584" alt="image" src="https://github.com/user-attachments/assets/61cabc44-1174-4493-9474-7b8960104993" />

## Added to sudo group:
```bash
sudo usermod -aG sudo adminuser
```
## Verifying Sudo Access:
```bash
sudo whoami
```
# 4. SSH Access Evidence (Remote Administration)

All system administration tasks were performed remotely from the workstation.
Example Remote Commands
```bash
hostname
uptime
whoami
```
<img width="709" height="186" alt="image" src="https://github.com/user-attachments/assets/8739b649-dfa7-493c-9eed-6f686f3033fb" />

# 5. Firewall Ruleset Documentation
Full Ruleset
```bash
sudo ufw status numbered
```
ip a

## Show service and process evidence

<img width="941" height="799" alt="Screenshot 2025-12-26 060919" src="https://github.com/user-attachments/assets/bccac398-ceac-4238-b581-eddd6fade496" />
<img width="683" height="206" alt="Screenshot 2025-12-26 060940" src="https://github.com/user-attachments/assets/821ae744-fe52-44a4-8b29-86b69b63474f" />

# Before/after comparison summary (what changed and why)
SSH (/etc/ssh/sshd_config)

Before

- Password auth may have been enabled by default.
- Root SSH login may have been permitted or “prohibit-password”.

After

- PasswordAuthentication no → reduces brute-force success risk.
- PermitRootLogin no → removes high-value target over the network.
- PubkeyAuthentication yes → uses asymmetric keys (stronger than passwords).

Firewall (UFW)

Before

- Inbound policy potentially open/undefined.

After

- Default deny incoming.
- Only allow TCP/22 from WORKSTATION_IP → tight network access control.

# Problems encountered + fixes 

Issue: Restarting SSH can lock you out if config is wrong.
Fix: Used sshd -t before restarting to validate syntax.

Issue: UFW enable may warn about disrupting SSH.
Fix: Ensured the allow rule for WORKSTATION_IP:22 was added before enabling.

# Reflection: security vs usability trade-off

Key-based auth is more secure than passwords, but it introduces usability overhead (key management, passphrases, and access recovery). Limiting SSH to a single workstation IP reduces attack surface significantly, but it can block legitimate access if the workstation IP changes so documenting IP addressing and having a controlled method to update rules is essential.

