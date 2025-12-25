## WEEK 3: APPLICATION SELECTION FOR PERFORMANCE TESTING  {#week-3}

### Overview
This week focused on selecting appropriate applications for comprehensive performance evaluation in Week 6. The selection prioritised applications representing diverse workload types to thoroughly assess system behaviour under various resource utilisation patterns.

### 3.1 Selection Criteria

Applications were selected based on their ability to represent different resource utilisation patterns (CPU, RAM, disk I/O, network), generate measurable and repeatable workloads, availability in Ubuntu repositories for easy installation, comprehensive documentation, and suitability for headless server environments.

### 3.2 Selected Applications

**Application 1: stress-ng (CPU-Intensive Workload)**

stress-ng is a CPU stress testing and performance benchmarking tool that generates controllable CPU load across specified cores. It supports various CPU stress methods including arithmetic operations and matrix calculations, provides precise control over test duration and intensity, and is widely used in industry for CPU performance validation.

The application generates 90-100% CPU usage during stress tests whilst maintaining low memory usage (under 50MB) and minimal disk I/O. It will be monitored using `top`, `mpstat`, and load average metrics during test execution.

Installation: `sudo apt install stress-ng -y`

Usage example: `stress-ng --cpu 2 --timeout 60s`

**Application 2: nginx (Server Application - Balanced Workload)**

nginx is an industry-standard web server that generates realistic server workloads combining CPU, memory, and network utilisation. It demonstrates typical server application resource usage and is suitable for testing concurrent connection handling, matching real-world production scenarios.

The application generates moderate CPU usage (20-40% under load), low to moderate memory consumption (100-200MB), low disk I/O for static file serving, and moderate to high network usage depending on request rates. Monitoring will track combined resource usage with `htop`, network connections with `ss`, and response times.

Installation: `sudo apt install nginx -y`

Usage: Service runs continuously after installation and startup

**Application 3: fio (I/O-Intensive Workload)**

fio is a flexible I/O workload generator supporting various access patterns with precise control over read/write ratios, block sizes, and queue depths. It generates detailed I/O performance statistics and is the standard tool for storage performance evaluation in enterprise environments.

The application produces very high disk I/O that saturates disk bandwidth whilst maintaining low to moderate CPU usage (10-30%) and low memory consumption (under 100MB). Monitoring will use `iostat -x 1` to track disk utilisation, await times, and throughput during tests.

Installation: `sudo apt install fio -y`

Usage example: `fio --name=test --size=1G --rw=randwrite --bs=4k --runtime=60`

**Application 4: iperf3 (Network-Intensive Workload)**

iperf3 is the standard tool for network performance measurement, testing TCP and UDP throughput between systems whilst providing detailed network performance metrics. It validates network configuration and measures maximum achievable bandwidth.

The application saturates network bandwidth whilst maintaining low to moderate CPU usage (5-20%) and minimal memory consumption (under 50MB) with no disk I/O. Network throughput will be monitored with `iftop`, packet rates with `nethogs`, and CPU impact will be observed.

Installation: `sudo apt install iperf3 -y`

Usage: Run server mode on Ubuntu Server (`iperf3 -s`) and client mode from workstation (`iperf3 -c 192.168.56.102`)

**Application 5: htop (System Monitoring Tool)**

htop is an interactive process viewer providing real-time system resource monitoring with colour-coded output for easy interpretation. It shows per-core CPU usage, memory breakdown, and process trees, making it essential for understanding overall system behaviour during performance tests.

The monitoring tool has minimal resource overhead with under 2% CPU usage and very low memory consumption (under 20MB), ensuring it doesn't impact the tests it's monitoring. It will run continuously during other application tests to observe real-time resource consumption.

Installation: `sudo apt install htop -y`

Usage: Run interactively with `htop` command

### 3.3 Testing Strategy

**Phase 1: Baseline Measurements**

Baseline measurements will establish idle system performance by monitoring CPU, memory, disk, and network metrics for 5 minutes with no applications running.

**Phase 2: Individual Application Testing**

Each application will be tested independently for 5-10 minutes whilst monitoring resource consumption with htop and specialised tools. Peak and average resource usage will be recorded for each application

**Phase 3: Combined Workload Testing**

Multiple applications will run simultaneously to observe resource contention and system behaviour under mixed loads. This will identify performance degradation thresholds when the system is under realistic combined workloads

**Phase 4: Analysis and Documentation**

Results will be analysed to identify system bottlenecks, understand workload characteristics, evaluate security control performance impact, and develop recommendations for system optimisation

### 3.4 Expected Outcomes

The testing will produce quantitative data including CPU utilisation percentages, memory consumption metrics, disk I/O throughput measurements, network bandwidth utilisation, and process statistics. Qualitative analysis will identify system bottlenecks, characterise workload behaviour, assess security control performance impacts, and generate optimisation recommendations.

### 3.5 Installation Documentation

All applications will be installed through SSH using the APT package manager. The installation will include stress-ng, nginx, fio, iperf3, and htop, along with additional monitoring tools including iotop for I/O monitoring by process, iftop for real-time network bandwidth monitoring, nethogs for network usage per process, and sysstat for performance monitoring tools including iostat and mpstat.

Installation command used:
```bash
sudo apt update
sudo apt install stress-ng nginx fio iperf3 htop iotop iftop nethogs sysstat -y
```

### 3.6 Preparation for Week 6

All applications have been identified with clear justifications, installation procedures are documented, monitoring strategies are established, and the testing methodology is designed. This preparation ensures Week 6 performance testing can proceed efficiently with consistent and reliable measurements.

[WEEK 4: INITIAL SYSTEM CONFIGURATION AND SECURITY IMPLEMENTATION](week4.md)

[GO BACK TO INDEX PAGE](index.md)
