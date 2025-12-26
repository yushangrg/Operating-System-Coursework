# Week 6
# 1. Introduction
This week focused on executing detailed performance testing and analyzing operating system behavior under different workloads. The goal was to understand how different types of applications impact system resources and identify potential bottlenecks in the Linux server configuration.

# 2. Testing Methodology
## 2.1 Performance Metrics Monitored

## Server-side (collected over SSH):
Tools used (what each measures)
- uptime, top/ps, pidstat → process + CPU behaviour
- free -h, /proc/meminfo → memory usage / caching
- iostat -xz 1, vmstat 1 → disk wait, I/O saturation, run queue
- ss -tulpn → sockets & service status

## Workload generators (run from workstation unless noted):

- CPU: stress-ng --cpu ...
- Memory: stress-ng --vm ...
- Disk I/O: fio ...
- Network throughput: iperf3
- Latency: ping, mtr
- Service response: curl -w, wrk (HTTP) or app-specific client

## 2.2 Testing Scenarios Implemented
For each application/service, I conducted:

Baseline Performance Testing: System performance under idle conditions
Application Load Testing: Performance under simulated workload
Performance Bottleneck Identification: Analysis of limiting factors
Optimization Testing: Implementation and validation of improvements

# 3. Application Performance Testing
## 3.1 CPU-Intensive Application: Stress Testing
```bash
# Baseline CPU performance
sysbench cpu --cpu-max-prime=20000 run

# Monitoring during stress test
mpstat -P ALL 1 10
```
<img width="746" height="714" alt="Screenshot 2025-12-26 005838" src="https://github.com/user-attachments/assets/8658e1a1-5211-41c4-8455-0ba271dee777" />

## 3.2 Memory-Intensive Application: Redis Server
```bash
# Memory testing configuration
redis-benchmark -t set,get -n 100000 -c 100

# Memory monitoring
free -h --si
vmstat 1 10
```
<img width="879" height="469" alt="Screenshot 2025-12-26 010212" src="https://github.com/user-attachments/assets/76af04f9-96eb-475b-829c-591cd32640e8" />

## 3.3 I/O-Intensive Application: Database Operations
Disk I/O testing
```bash
dd if=/dev/zero of=testfile bs=1G count=1 oflag=direct
```
<img width="856" height="112" alt="Screenshot 2025-12-26 011936" src="https://github.com/user-attachments/assets/cee5baad-640e-4c82-abea-6eca1c440957" />

# 3.4 Network-Intensive Application: Web Server (nginx)
```bash
ping -c 20 192.168.56.101
```
<img width="799" height="573" alt="Screenshot 2025-12-26 063037" src="https://github.com/user-attachments/assets/8d424a42-5f8f-4914-816c-046e74c57679" />

# 4. Performance Data Table


