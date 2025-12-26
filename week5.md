# Week 5
Overview

In Week 5, advanced security mechanisms and monitoring capabilities were implemented on the Linux server. This phase focused on enforcing mandatory access control, automated security maintenance, intrusion prevention, and system verification through scripting. Additionally, a remote monitoring script was developed on the workstation to collect live performance metrics over SSH.

These implementations strengthen the system’s security posture while maintaining visibility into operating system behaviour.

# 1. Mandatory Access Control – AppArmor
Justification

Ubuntu Server uses AppArmor by default, providing mandatory access control (MAC) by restricting how applications interact with system resources. AppArmor profiles reduce the impact of compromised services by enforcing the principle of least privilege.

AppArmor Status Check
```bash
sudo apparmor_status
```
Observed Output (Summary):
-AppArmor enabled
-Profiles loaded
- Key services (e.g. sshd) running in enforce mode
<img width="588" height="786" alt="Screenshot 2025-12-25 231558" src="https://github.com/user-attachments/assets/7e1fa979-1f86-4754-a0df-d131d7e531f9" />

# 2. Automatic Security Updates
Configuration

To ensure timely installation of security patches, unattended-upgrades was installed and configured.
Install and Configure unattended-upgrades
```bash
sudo apt update && sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```
<img width="664" height="243" alt="Screenshot 2025-12-25 232656" src="https://github.com/user-attachments/assets/9f6e459d-0870-4155-b676-b18dcd41a753" />

Verification
<img width="1252" height="798" alt="Screenshot 2025-12-25 233544" src="https://github.com/user-attachments/assets/47457f43-9f15-4e3a-9fa6-f8abb188a8fd" />

# 3. Intrusion Prevention – Fail2Ban
Purpose

Fail2Ban monitors authentication logs and temporarily bans IP addresses exhibiting suspicious behaviour such as repeated failed login attempts.

Install Fail2ban
```bash
sudo apt install fail2ban
```
## Configure Jail for SSH
Edited ```/etc/fail2ban/jail.local``` to set:

bantime = 600

findtime = 300

maxretry = 3

```bash
sudo systemctl restart fail2ban
sudo systemctl status fail2ban
```
<img width="1262" height="276" alt="Screenshot 2025-12-25 234339" src="https://github.com/user-attachments/assets/45ba8837-84ac-46e7-abe2-3d69568217b4" />

# 4. Security Baseline Verification Script
Purpose

The security-baseline.sh script verifies that all security controls from Weeks 4 and 5 are correctly configured.

Script: ```security-baseline.sh```
```bash
#!/bin/bash
# security-baseline.sh
# Verifies firewall, SSH hardening, AppArmor, automatic updates, and fail2ban

echo "=== SECURITY BASELINE VERIFICATION ==="

echo "[1] Firewall status:"
sudo ufw status verbose

echo "[2] SSH password authentication:"
grep "^PasswordAuthentication" /etc/ssh/sshd_config

echo "[3] Root login over SSH:"
grep "^PermitRootLogin" /etc/ssh/sshd_config

echo "[4] AppArmor status:"
sudo aa-status | head -n 10

echo "[5] Unattended upgrades status:"
systemctl is-enabled unattended-upgrades

echo "[6] Fail2Ban status:"
sudo fail2ban-client status sshd

echo "=== BASELINE CHECK COMPLETE ==="
```

Execution (via SSH)
```bash
chmod +x security-baseline.sh
./security-baseline.sh
```
![WhatsApp Image 2025-12-26 at 00 31 42](https://github.com/user-attachments/assets/d1a2891b-4b73-40e7-840d-a156a075aa3d)

# 5. Remote Monitoring Script
Purpose

The monitor-server.sh script runs on the workstation, connects to the server via SSH, and retrieves live performance metrics.

Metrics Collected

-CPU load
-Memory usage
-Disk usage
-System uptime

Script: ```monitor-server.sh```
```bash
#!/bin/bash
# monitor-server.sh
# Collects performance metrics from remote server via SSH

SERVER_USER="adminuser"
SERVER_IP="192.168.56.10"

echo "=== REMOTE SERVER MONITORING ==="
ssh ${SERVER_USER}@${SERVER_IP} << EOF
echo "Hostname:"
hostname

echo "Uptime:"
uptime

echo "CPU Load:"
cat /proc/loadavg

echo "Memory Usage:"
free -h

echo "Disk Usage:"
df -h /
EOF

echo "=== MONITORING COMPLETE ==="
```
## Execution:
```bash
chmod +x monitor-server.sh
./monitor-server.sh
```
<img width="1597" height="953" alt="image" src="https://github.com/user-attachments/assets/8fd10ff1-214c-43dc-a40c-ba15b39bd267" />

# 6. Security vs Performance Considerations
| Control | Security Benefit | Performance Impact |
| :--- | :--- | :--- |
| AppArmor | Limits damage from compromised services | Negligible |
| Unattended Upgrades | Reduces vulnerability window | Minor during updates |
| Fail2Ban | Prevents brute-force attacks | Minimal |
| Firewall (UFW) | Network traffic restriction | Negligible |
The security mechanisms introduced provide significant protection while maintaining system efficiency.

# 7. Reflection

This week highlighted how modern operating systems balance security enforcement with system usability and performance. Mandatory access control and automated security tooling significantly reduce administrative risk while maintaining system responsiveness. Developing verification and monitoring scripts improved command-line proficiency and demonstrated how automation supports reliable system administration.
