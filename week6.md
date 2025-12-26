# Week 6
# 1. Introduction
This week focused on executing detailed performance testing and analyzing operating system behavior under different workloads. The goal was to understand how different types of applications impact system resources and identify potential bottlenecks in the Linux server configuration.

# 2. Testing Methodology
## 2.1 Performance Metrics Monitored

For each selected application/service, I monitored and compared the following metrics:

- CPU Usage: Measured using top, htop, and mpstat
- Memory Usage: Tracked with free, vmstat, and process-specific monitoring
- Disk I/O Performance: Evaluated using iotop, iostat, and dd commands
- Network Performance: Measured with iperf3, netstat, and iftop
- System Latency: Monitored using ping and custom response time scripts
- Service Response Times: Tracked with curl timing and application-specific metrics

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
iperf3 -c <server_ip> -t 30
ab -n 10000 -c 100 http://localhost/
```
Results:
- Baseline network latency: 0.8ms
- Under load: Average latency 4.2ms, peaks at 12ms
- Throughput: Sustained 850Mbps, 92% of theoretical maximum

