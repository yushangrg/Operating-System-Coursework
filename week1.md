# Week 1
# 1. System architecture diagram
<img width="1350" height="270" alt="Screenshot 2025-12-26 045133" src="https://github.com/user-attachments/assets/c35d12a5-f984-4d46-a112-2a147d01e30e" />

# 2. Distribution selection justification 
Chosen server distribution: Ubuntu Server (LTS)

## Why this choice:

- Strong documentation, large community support, lots of examples for: SSH hardening, ufw firewall, fail2ban, unattended-upgrades
- Stable base for security + performance experiments
- Easy package availability via apt

Alternatives considered

Debian

- Pros: very stable, minimal, trusted in production
- Cons: packages may be older; sometimes less “out-of-the-box” tooling compared to Ubuntu for beginners

Rocky Linux / AlmaLinux

- Pros: RHEL-like, enterprise-style security tooling (SELinux-first)
- Cons: learning curve differs from Ubuntu-based guides; package names and defaults differ (may slow down coursework execution)

Decision summary
Ubuntu Server LTS gives the best balance of:

- predictable stability
- easiest documentation trail for required tasks
- fast setup for SSH-only administration

# 3. Workstation configuration decision (Option A/B/C)
Chosen workstation option: Option B – Host machine as workstation

Reasoning

- Faster workflow: no nested VM/VM networking complexity.
- Uses the host’s terminal + SSH client.
- Easier to record demonstration (screen capture is simpler).

Trade-offs

- Host environment differences could affect tooling (e.g., Windows vs Linux shell behavior).
Mitigation: use consistent tools (PowerShell + OpenSSH on Windows, or WSL) and document commands clearly.

# 4. Network configuration documentation 


Host-Only Network (good for isolated security testing)

- Server and workstation can talk inside a private network
- No access from outside your machine unless you configure it
- Fits the “isolated environment” expectation for security tools later

## NAT Network:

- VMs can access the internet for updates
- Still allows VM-to-VM communication if both are on same NAT Network
- You must confirm SSH reaches the server from workstation

## IP Addressing plan (example – replace with your real IPs):

| Device        | Network Interface        | IP Address           | Description / Notes                    |
|---------------|--------------------------|----------------------|----------------------------------------|
| Workstation   | enp0s3 (example)          | 192.168.56.10/24     | SSH client, system monitoring           |
| Server VM     | enp0s3 (example)          | 192.168.56.20/24     | SSH server (port 22 enabled)            |
| Gateway       | N/A                      | N/A                  | Host-only network usually has no gateway|

# 5. System Specification
<img width="982" height="566" alt="Screenshot 2025-12-26 050610" src="https://github.com/user-attachments/assets/9973153b-5ed5-41b4-979c-ad0e96e14899" />

# 6. Short reflection 

This week focused on planning a professional-style remote admin setup: a headless server managed only over SSH from a separate workstation. The main trade-off is convenience vs realism headless administration is less comfortable than GUI management, but it better reflects real server operations and forces stronger CLI skills. 

