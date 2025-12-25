# Week 3
# 3.1 Purpose of Application Selection

The aim of Week 3 is to select a range of applications that represent different workload types in order to evaluate how the Linux operating system behaves under varying resource demands.
By testing CPU, memory, disk I/O, and network-intensive workloads, I can later analyse operating system performance, identify bottlenecks, and evaluate trade-offs between performance, security, and resource utilisation.

All applications selected are command-line based, suitable for a headless Linux server, and installable remotely via SSH, aligning with professional system administration practices.

# 3.2 Application Selection Matrix

The table below summarises the selected applications, the workload type they represent, and the justification for choosing each one.

| Application | Workload Type      | Description                                           | Justification                                                                           |
| ----------- | ------------------ | ----------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `stress-ng` | CPU-intensive      | Generates high CPU load using multiple worker threads | Useful for analysing CPU scheduling, load handling, and system stability under stress   |
| `stress-ng` | RAM-intensive      | Allocates and consumes system memory                  | Allows observation of memory usage, swapping behaviour, and RAM limits                  |
| `fio`       | Disk I/O-intensive | Performs configurable read/write disk operations      | Ideal for measuring filesystem performance and disk throughput                          |
| `iperf3`    | Network-intensive  | Measures network bandwidth and throughput             | Used to analyse network performance and latency over SSH connections                    |
| `nginx`     | Server application | Lightweight web server                                | Represents a real-world server workload and allows monitoring of service response times |

# 3.3 Installation Documentation (SSH-based)

All installations were performed remotely via SSH on the server using the package manager.

Update Package Index
```bash
sudo apt update
```

Install stress-ng
```bash
sudo apt install stress-ng -y
```

Install fio
```bash
sudo apt install fio -y
```

Install iperf3
```bash
sudo apt install iperf3 -y
```

Install nginx
```bash
sudo apt install nginx -y
```

After installation, each application was verified using version or status commands:

stress-ng --version
fio --version
iperf3 --version
systemctl status nginx

<img width="1196" height="703" alt="Screenshot 2025-12-25 205738" src="https://github.com/user-attachments/assets/66e48bf4-ddcc-44f6-882b-4d1a77f482cb" />

# 3.4 Expected Resource Profiles

Before testing, expected system behaviour for each workload was identified to provide a baseline for later comparison.

## CPU-intensive (stress-ng)

High CPU utilisation across one or more cores

Increased system load average

Minimal disk and network usage

## RAM-intensive (stress-ng)

Rapid memory allocation

Possible swap usage if RAM limits are exceeded

Increased memory pressure visible in system monitors

## Disk I/O-intensive (fio)

High read/write activity

Increased disk latency

Potential impact on other processes accessing the filesystem

## Network-intensive (iperf3)

High network throughput

Increased packet transmission

Potential SSH latency under heavy load

## Server Application (nginx)

Moderate CPU and memory usage

Network traffic dependent on client requests

Consistent background service activity

# 3.5 Monitoring Strategy

To accurately measure system performance, a monitoring strategy was planned for each workload.


| Resource            | Monitoring Tools        | Purpose                                               |
| ------------------- | ----------------------- | ----------------------------------------------------- |
| CPU usage           | `top`, `htop`, `mpstat` | Observe CPU load, process scheduling, and utilisation |
| Memory usage        | `free -h`, `vmstat`     | Monitor RAM usage and swap behaviour                  |
| Disk I/O            | `iostat`, `fio` output  | Measure disk throughput and latency                   |
| Network performance | `iperf3`, `ping`        | Measure bandwidth, latency, and packet loss           |
| Services            | `systemctl`, `ss`       | Verify service status and active connections          |

All monitoring will be performed remotely via SSH, reinforcing command-line proficiency and real-world server administration skills.

# 3.6 Reflection

This week helped reinforce the importance of selecting representative workloads when evaluating operating system performance.
Rather than relying on a single benchmark, using multiple applications allows for a more holistic understanding of how the OS manages hardware resources under different conditions.

This structured selection and planning stage provides a solid foundation for Week 6 performance testing, where quantitative data will be collected, analysed, and optimised.
