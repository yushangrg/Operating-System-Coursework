# Week 2
## 1. Performance Testing Plan
Remote Monitoring Methodology

The server system runs headless and is accessed only via SSH from the workstation. All monitoring and testing will therefore be performed remotely using command-line tools.

Monitoring approach:

Monitoring commands will be executed via SSH from the workstation.

Lightweight CLI tools are used to avoid distorting performance results.

Measurements are captured during:

Idle baseline

Active workload execution

Post-optimisation testing

Data collection principles:

Use consistent time intervals

Record numerical outputs

Compare results before and after optimisation

Store results in structured tables

Performance Metrics to be Collected
Metric	Tool(s)	Purpose
CPU usage	top, htop, mpstat	Identify CPU-bound workloads
Memory usage	free -h, vmstat	Detect memory pressure and swapping
Disk I/O	iostat, df -h	Measure read/write bottlenecks
Network	ping, iperf3	Measure latency and throughput
Process load	ps, top	Analyse process-level resource usage
System uptime	uptime	Verify system stability
Testing Stages

Baseline Testing

No workload running

Captures idle system state

Load Testing

Application or service running

Measures peak resource usage

Bottleneck Identification

Identify CPU, RAM, disk, or network constraints

Optimisation Testing

Apply tuning (e.g. service configuration changes)

Measure quantitative improvement
## 2. Security Configuration Checklist
This checklist defines the minimum security baseline that the server must meet before being considered production-ready.

SSH Hardening

Disable root login

Use key-based authentication

Disable password authentication

Restrict SSH access to one trusted workstation IP

Change default SSH configuration settings

Firewall Configuration

Enable firewall (UFW)

Allow SSH from one specific IP only

Deny all other incoming traffic

Log denied connections

Mandatory Access Control

Use AppArmor (Ubuntu default)

Ensure enforced mode is enabled

Verify active security profiles

Automatic Updates

Enable unattended security upgrades

Ensure critical patches are applied automatically

Verify update logs

User Privilege Management

Create a non-root administrative user

Use sudo for privilege escalation

Remove unnecessary user accounts

Enforce strong password policies

Network Security

Minimise exposed services

Disable unused ports

Verify listening services using ss or netstat
## 3. Threat Model
Threat 1: Brute-Force SSH Attacks

Description:
Attackers attempt repeated login attempts over SSH to guess credentials.

Impact:

Unauthorized system access

Data compromise

Full system takeover if root access is obtained

Mitigation Strategies:

Key-based SSH authentication

Disable password authentication

Use firewall IP restrictions

Implement fail2ban (later phase)

Threat 2: Privilege Escalation

Description:
An attacker gains access to a low-privileged user account and escalates privileges.

Impact:

Root-level compromise

Modification of system files

Persistence mechanisms installed

Mitigation Strategies:

Principle of least privilege

Controlled sudo access

Mandatory access control (AppArmor)

Regular auditing of users and permissions

Threat 3: Unpatched Vulnerabilities

Description:
Outdated packages contain known security vulnerabilities.

Impact:

Remote code execution

Denial of service

Exploitation of known CVEs

Mitigation Strategies:

Automatic security updates

Regular system updates

Vulnerability scanning 
