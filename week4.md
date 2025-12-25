## Week 4
Overview

This week focuses on deploying the Linux server and implementing foundational security controls. All administrative tasks were performed remotely via SSH from the workstation system, in line with the coursework constraints. The objective was to harden remote access, restrict network exposure, and enforce proper privilege management.

# 1. SSH Key-Based Authentication Configuration
Objective: Replace password authentication with key-based authentication for enhanced security.

## 1. Generated SSH key pair on workstation:
```bash
ssh-keygen -t rsa -b 4096 -C "student_id@workstation"
```
## 2.Copied public key to server:
```bash
ssh-copy-id student@192.168.x.x
```
## 3.Modified SSH configuration on server:
```bash
sudo nano /etc/ssh/sshd_config
```
## 4.Changed settings:

PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no

## 5.Restarted SSH service:
```bash
sudo systemctl restart sshd
```
