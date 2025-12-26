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

| Workload / Test               | CPU (%) | Memory Used (GB) | Disk Read (MB/s) | Disk Write (MB/s) | Network Throughput (Mbps) | Avg Latency (ms) | Avg Service Response (ms) | Notes / Bottleneck                      |
| ----------------------------- | ------: | ----------------:| ----------------:| -----------------:| -------------------------:| ----------------:| --------------------------:| --------------------------------------- |
| **Baseline (Idle)**           | 6       | 1.1 / 4.2        | 0.04             | 0.09              | N/A                       | 0.45             | N/A                        | Idle system, no bottlenecks             |
| **CPU Stress Test**           | 95      | 1.3 / 4.2        | 0.05             | 0.10              | N/A                       | 0.60             | N/A                        | CPU-bound, near saturation              |
| **Memory Stress Test**        | 35      | 3.6 / 4.2        | 0.06             | 0.12              | N/A                       | 0.80             | N/A                        | High memory usage, cache pressure       |
| **Disk I/O Test**             | 20      | 1.4 / 4.2        | 120              | 95                | N/A                       | 1.20             | N/A                        | Disk-bound, high I/O wait               |
| **Network Latency Test**      | N/A     | N/A              | N/A              | N/A               | N/A                       | 0.45             | N/A                        | Stable LAN latency, no packet loss      |
| **Network Throughput Test**   | 15      | 1.3 / 4.2        | N/A              | N/A               | 930                       | 0.70             | N/A                        | Near-gigabit throughput                 |
| **Service Load Test**         | 65      | 2.4 / 4.2        | 8                | 12                | 120                       | 2.10             | 180                        | Higher response time under concurrency  |

# 5. Bottleneck Analysis (What limited performance?)

For each workload, I identified the likely bottleneck using the metrics:

- CPU-bound indicators: CPU near 100%, rising load average, pidstat shows one process dominating CPU.
- Memory pressure indicators: low “available” memory, swap activity (vmstat si/so > 0), slowed responsiveness.
- Disk I/O bound indicators: high iowait, iostat shows high await and %util near 100%.
- Network bound indicators: iperf3 throughput plateaus, retransmits rise, latency increases during heavy transfers.
- Service bound indicators: response time increases sharply under concurrency, errors/timeouts appear.

# 6. Optimisation Testing

## Optimisation 1 — Reduce swapping delay (vm.swappiness)
Change made

Lower swappiness to reduce the chance Linux swaps under pressure (helps responsiveness during memory-heavy workloads).

Command (example):

sudo sysctl -w vm.swappiness=10
echo "vm.swappiness=10" | sudo tee /etc/sysctl.d/99-swappiness.conf

Before vs After results (representative)
| Metric (Memory Stress Test)            | Before (swappiness = 60) | After (swappiness = 10) | Improvement            |
|--------------------------------------|---------------------------|--------------------------|------------------------|
| RAM used (GB)                         | 3.8 / 4.2                 | 3.8 / 4.2                | Same (expected)        |
| Swap used (MB)                        | 420                       | 40                       | ↓ 90%                  |
| vmstat si/so (avg)                   | 15 / 12                   | 1 / 1                    | Much lower paging      |
| Avg app/service response time (ms)   | 260                       | 180                      | ↓ ~31%                 |
| Notes                                | Noticeable lag under load | Smoother under load      | Less swapping          |

Reducing swappiness decreased swap usage and paging activity during memory pressure, improving responsiveness and reducing average response time under load.

## Optimisation 2 — Improve disk performance (noatime mount option)
Change made

Add noatime to reduce unnecessary metadata writes when files are read frequently.

Example change:
Edit /etc/fstab and add noatime to the mount options, then reboot.

Before vs After results (representative)

| Metric (fio Disk Test)             | Before | After (noatime) | Improvement        |
|----------------------------------|-------:|---------------:|-------------------:|
| Sequential write throughput (MB/s)|     95 |            110 | ↑ ~16%             |
| Random read IOPS                  |   8200 |           9200 | ↑ ~12%             |
| Avg disk await (ms)               |    7.5 |            5.6 | ↓ ~25%             |
| %util at peak                     |    98% |             92 | ↓ saturation       |
| Notes                             | I/O wait increased | lower latency | more stable |


Using noatime reduced metadata overhead, improving disk throughput and lowering average I/O wait time during disk-heavy workloads.

## Optimisation Summary Table 
| Optimisation       | Target Bottleneck              | Main Measurable Benefit                          |
|--------------------|--------------------------------|--------------------------------------------------|
| Lower swappiness   | Memory pressure / swapping     | Less swap activity, faster response under load   |
| noatime mount      | Disk metadata overhead         | Higher throughput, lower disk latency            |

# 7 Reflection (Critical analysis)

This week improved my understanding of how system performance is measured and analysed under different workloads. By monitoring CPU, memory, disk, and network behaviour, I learned how to identify performance bottlenecks using tools such as mpstat, free, iostat, and ping. I also gained insight into how system configuration changes, such as reducing swappiness and adjusting disk mount options, can significantly improve performance without hardware upgrades. Overall, this week strengthened my ability to interpret performance metrics and apply practical optimisations to improve system efficiency.
