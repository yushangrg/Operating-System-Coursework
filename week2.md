# Week 2
Overview

This week focuses on designing a security baseline and a performance testing methodology for the Linux server.
Before implementing any security controls, it is essential to plan what will be protected, why it must be protected, and how performance and security will be measured objectively.

The outcome of this week is a clear, structured plan that will guide all security implementations and performance testing in later weeks.

## 1. Performance Testing Plan
Objective

The objective of performance testing is to evaluate how the Linux operating system behaves under different workloads while being administered remotely via SSH. The testing focuses on CPU usage, memory usage, disk I/O, network performance, and system responsiveness.

Remote Monitoring Methodology

All monitoring will be performed remotely from the workstation system using SSH.
This reflects real-world server administration practices and ensures compliance with the coursework requirement that the server is managed headlessly.

Monitoring Approach

The following tools will be used to monitor system performance:

| Resource Type | Tool(s) Used | Purpose |
| :--- | :--- | :--- |
| CPU Usage | `top`, `htop`, `mpstat` | Monitor real-time and average CPU load |
| Memory Usage | `free -h`, `vmstat` | Analyse RAM and swap utilisation |
| Disk Usage | `df -h`, `iostat` | Measure disk space and I/O performance |
| Network Performance | `ping`, `ss`, `iftop` | Measure latency, throughput, and connections |
| System Load | `uptime`, `top` | Evaluate load averages over time |

Testing Stages

Performance testing will be conducted in the following stages:

Baseline Testing
Measure system performance immediately after installation with minimal services running.

Load Testing
Measure performance while running selected applications representing different workload types (CPU, memory, I/O, and network intensive).

Optimisation Testing
Apply system tuning and configuration improvements, then re-measure performance to quantify improvements.

Data Collection

Performance data will be collected manually and via scripts, recorded in tables, and visualised using charts in later weeks.

## 2. Security Configuration Checklist

The following checklist defines the minimum security baseline for the Linux server.
Each control is justified based on common Linux server security best practices.

SSH Hardening

Disable root login over SSH

Enforce key-based authentication

Disable password authentication

Restrict SSH access to a single trusted workstation IP

Use a non-default SSH port (where appropriate)

Firewall Configuration

Enable firewall (UFW)

Allow SSH only from the workstation IP

Deny all other incoming connections by default

Log dropped packets for audit purposes

Mandatory Access Control (MAC)

Enable AppArmor (default on Ubuntu Server)

Ensure profiles are enforced, not in complain mode

Monitor AppArmor logs for denied actions

Automatic Updates

Enable unattended security updates

Ensure only security patches are installed automatically

Verify update logs regularly

User Privilege Management

Create a non-root administrative user

Use sudo for privileged commands

Remove unnecessary users and groups

Apply least privilege principle

Network Security

Disable unused network services

Verify listening ports using ss or netstat

Prevent unnecessary external exposure

## 3. Threat Model

This threat model identifies realistic threats to the Linux server and documents mitigation strategies aligned with the planned security controls.

## Threat 1: Brute-Force SSH Attacks

## Description:
Attackers may attempt repeated login attempts to gain unauthorised SSH access.

## Impact:
Compromise of the server, data theft, or system misuse.

## Mitigation:

Key-based SSH authentication

Password authentication disabled

Firewall rules restricting SSH access

Fail2ban (implemented in later weeks)

## Threat 2: Privilege Escalation

## Description:
A compromised user account could attempt to gain root privileges.

## Impact:
Full system compromise and loss of control.

## Mitigation:

Use of non-root user accounts

Controlled sudo access

Mandatory Access Control (AppArmor)

Regular auditing of user privileges

## Threat 3: Unauthorised Network Access

## Description:
Open ports or unnecessary services may expose the server to network-based attacks.

## Impact:
Remote exploitation of services or denial-of-service attacks.

## Mitigation:

Firewall with default deny rules

Minimal service installation

Regular port scanning and service review

## Reflection

This week highlighted the importance of planning before implementation.
By defining a security baseline and performance testing methodology early, later configuration decisions can be evaluated objectively rather than subjectively.

The threat modelling exercise reinforced how common attacks exploit weak authentication, excessive privileges, and poor network controls.
Designing mitigations in advance ensures that security controls are implemented systematically rather than reactively.
