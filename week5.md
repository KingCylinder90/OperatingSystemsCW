## WEEK 5: ADVANCED SECURITY AND MONITORING INFRASTRUCTURE {#week-5}

### Overview
Week 5 focused on implementing advanced security controls building upon the Week 4 foundation. This included verifying AppArmor mandatory access control, configuring automatic security updates, deploying fail2ban intrusion detection, and creating monitoring scripts for ongoing security verification.

### 5.1 AppArmor Mandatory Access Control

**Objective:** Verify AppArmor is enabled and enforcing security policies on the system.

#### AppArmor Status Verification

![AppArmor Status](images/week5-apparmor-status.png)

AppArmor status was checked using `sudo aa-status | head -20`. The output shows:

**AppArmor Summary:**
- AppArmor module is loaded
- 54 profiles loaded
- 54 profiles in enforce mode
- 0 profiles in complain mode
- 8 processes with profiles defined
- 8 processes in enforce mode

AppArmor is active and enforcing security policies. The 54 loaded profiles provide mandatory access control for critical system services. All profiles are in enforce mode, meaning violations are blocked rather than just logged.

**Profile Examples:**
- `/snap/snapd/...` (Snap package isolation)
- `/usr/bin/...` (System binary confinement)
- Various system services

#### AppArmor Enabled Check

![AppArmor Enabled](images/week5-apparmor-enabled.png)

AppArmor was verified as enabled using `sudo aa-enabled && echo "AppArmor is enabled" || echo "AppArmor is disabled"`. The output confirms "AppArmor is enabled".

**Security Benefit:** AppArmor provides mandatory access control that limits what processes can do. Even if a service is compromised, AppArmor profiles restrict file access, network connections, and system capabilities. This containment reduces the impact of successful exploits.

### 5.2 Automatic Security Updates

**Objective:** Configure automatic installation of security patches to ensure the system remains protected against known vulnerabilities.

#### Unattended Upgrades Installation

![Unattended Upgrades Install](images/week5-unattended-upgrades-install.png)

The unattended-upgrades package was installed using `sudo apt install unattended-upgrades -y`. The installation completed successfully.

**Package Purpose:** The unattended-upgrades package automatically downloads and installs security updates without manual intervention. This ensures critical patches are applied promptly, reducing the window of vulnerability.

#### Configuration Verification

![Auto Upgrades Config](images/week5-auto-upgrades-config.png)

Automatic update configuration was verified using `cat /etc/apt/apt.conf.d/20auto-upgrades`:

**Configuration Settings:**
```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

**Configuration Analysis:**
- `Update-Package-Lists "1"`: Package lists updated daily
- `Unattended-Upgrade "1"`: Security updates installed automatically daily

This configuration ensures the system checks for and installs security updates every 24 hours. Only security updates are installed automatically. Regular package updates still require manual approval.

### 5.3 Fail2ban Intrusion Detection

**Objective:** Deploy fail2ban to detect and block SSH brute force attempts automatically.

Fail2ban was installed using `sudo apt install fail2ban -y`. The package installed successfully along with its dependencies.

**Purpose:** Fail2ban monitors log files for failed authentication attempts. When a threshold is exceeded, it automatically creates firewall rules to block the offending IP address for a specified duration.

#### Fail2ban Service Status

![Fail2ban Status](images/week5-fail2ban-status.png)

Service status was verified using `sudo systemctl status fail2ban`:

**Service Details:**
- Status: Active (running)
- Process ID: 3847
- Memory usage: 13.7M
- Loaded configuration: `/etc/fail2ban/jail.conf`

The service is operational and monitoring for intrusion attempts.

#### Fail2ban Client Status

![Fail2ban Client Status](images/week5-fail2ban-client-status.png)

Overall fail2ban status was checked using `sudo fail2ban-client status`:

**Active Jails:**
```
Number of jail: 1
Jail list: sshd
```

One jail is active. The sshd jail monitors SSH authentication attempts.

#### SSH Jail Status

![Fail2ban SSH Jail](images/week5-faul2ban-sshd-status.png)

The SSH jail was examined using `sudo fail2ban-client status sshd`:

**SSH Jail Configuration:**
- Filter: sshd (monitors SSH logs)
- Currently failed: 0
- Total failed: 0
- Currently banned: 0
- Total banned: 0

**Configuration Details:**
- Actions: `iptables-multiport` (creates firewall rules)
- Log path: `/var/log/auth.log` (SSH authentication log)

No failed attempts or banned IPs at this time. The jail is active and will automatically ban IPs after 5 failed authentication attempts within 10 minutes. Banned IPs are blocked for 10 minutes by default.

### 5.4 Security Baseline Verification Script

**Objective:** Create an automated script to verify all security configurations from Weeks 4 and 5.

#### Script Execution

![Security Baseline Output1](images/week5-security-baseline-output-1.png)

![Security Baseline Output2](images/week5-security-baseline-output-2.png)

The security baseline script was executed using `sudo ./security-baseline.sh`. The script performs automated security checks across all implemented controls.

**Script Results Summary:**

**SSH Security Checks:**
- ✓ SSH service is running
- ✓ Root login is disabled
- ✓ Password authentication is disabled
- ✓ Public key authentication is enabled

**Firewall Checks:**
- ✓ UFW firewall is installed
- ✓ UFW firewall is active
- ✓ SSH firewall rules configured

**User Management Checks:**
- ✓ Admin user exists
- ✓ Admin has sudo privileges

**AppArmor Checks:**
- ✓ AppArmor is installed
- ✓ AppArmor is enabled

**Automatic Updates Checks:**
- ✓ Unattended-upgrades installed

**Fail2ban Checks:**
- ✓ Fail2ban is installed
- ✓ Fail2ban service is running
- ✓ Fail2ban SSH jail is active

**Summary:**
- Passed: 15 checks
- Failed: 0 checks
- Security Compliance: 100%
- Status: EXCELLENT

All security controls are operational and properly configured. The automated verification confirms the security baseline is maintained.

### 5.5 Remote Monitoring Script

**Objective:** Create a monitoring script that runs from the workstation to collect server metrics remotely.

#### Monitoring Script Execution

![Monitoring Script Output1](images/week5-monitor1.png)

![Monitoring Script Output2](images/week5-monitor2.png)

The monitoring script was executed from the Linux Mint workstation using `./monitor-server.sh 192.168.56.102`. The script connects via SSH and collects comprehensive system metrics.

**System Information:**
- Hostname: ubuntu-server
- Kernel: 6.8.0-90-generic
- OS: Ubuntu 24.04.3 LTS
- Uptime: up 1 hour, 38 minutes

**CPU Metrics:**
- Model: AMD Ryzen 9 7945HX with Radeon Graphics
- Cores: 1
- Usage: 0.0%
- Load Average: 0.06, 0.02, 0.00

**Memory Metrics:**
- Total: 1.9Gi
- Used: 328Mi
- Available: 1.6Gi
- Usage: 16.7%
- Swap Total: 2.0Gi
- Swap Used: 0B

**Disk Metrics:**
- Size: 12G
- Used: 4.9G
- Available: 5.8G
- Usage: 46%

**Network:**
- Active Interfaces: enp0s3 (192.168.56.102/24)
- Listening Ports: 22 (SSH), 53 (DNS), 80, 5201

**Processes:**
- Total: 111

**Top 5 CPU Processes:**
Shows processes with highest CPU usage

**Top 5 Memory Processes:**
Shows processes with highest memory usage

**Security Services:**
- SSH: Active
- UFW: Active
- Fail2ban: Active
- AppArmor: Enabled

All security services are operational. System performance is healthy with low resource utilisation. The script successfully demonstrates remote monitoring capabilities via SSH.

### 5.6 Security Configuration Summary

**Implemented Controls:**

**Mandatory Access Control:**
- AppArmor enabled with 54 profiles enforcing
- All profiles in enforce mode
- 8 processes confined

**Patch Management:**
- Unattended-upgrades configured
- Daily security update checks
- Automatic installation enabled

**Intrusion Detection:**
- Fail2ban installed and active
- SSH jail monitoring authentication attempts
- Automatic IP banning configured

**Monitoring Infrastructure:**
- Security baseline verification script deployed
- Remote monitoring script operational
- Automated security status checking

### 5.7 Key Learning Points

**AppArmor Understanding:**
- Mandatory access control concepts
- Enforce mode vs complain mode
- Profile management
- Security policy verification

**Automated Patch Management:**
- Unattended-upgrades configuration
- Security update automation
- Update scheduling
- Configuration file locations

**Intrusion Detection Systems:**
- Fail2ban architecture and operation
- Jail configuration
- Log monitoring mechanisms
- Automatic response actions

**Security Automation:**
- Bash script development
- SSH remote execution
- Automated verification procedures
- System monitoring techniques

### 5.8 Challenges Encountered

**Fail2ban Installation:**

Initial network connectivity issues prevented package installation.

**Solution:** Temporarily switched VM network adapter to NAT mode for internet access. Installed all required packages. Switched back to Host-Only network for secure operation.

**Remote Monitoring Permissions:**

Monitoring script required sudo permissions for UFW and AppArmor status checks.

**Solution:** Configured specific sudo permissions for monitoring commands. This allows status checks without granting full sudo access to remote commands.

**Script File Permissions:**

Scripts copied from shared folder had incorrect ownership and permissions.

**Solution:** Used `sudo chown` to fix ownership and `chmod 755` to set correct execute permissions.

### 5.9 Critical Reflections

**AppArmor vs SELinux:**

Ubuntu uses AppArmor by default rather than SELinux. AppArmor is simpler to configure and uses path-based policies. SELinux provides more granular control but has a steeper learning curve. For this coursework, AppArmor provides adequate mandatory access control with easier management.

**Automatic Update Risks:**

Automatic security updates improve security posture but carry minimal risk. Updates occasionally break configurations or introduce bugs. However, the security benefit of rapid patching outweighs this risk. Production environments often use staged rollouts with testing before full deployment.

**Fail2ban Effectiveness:**

Fail2ban effectively blocks brute force attacks but is reactive rather than proactive. It only acts after failed attempts occur. The Week 4 configuration (key-based auth only) is more effective as it prevents password attacks entirely. Fail2ban provides an additional layer of defence in case SSH configuration is weakened.

**Monitoring Script Security:**

The monitoring script uses SSH with key-based authentication for security. All data is transmitted encrypted. However, the script requires some sudo access on the remote system. This is necessary for comprehensive monitoring but increases the privileges available to the workstation.

### 5.10 Script Functionality

**Security Baseline Script:**

The script performs 15 automated checks covering SSH configuration, firewall status, user management, AppArmor, automatic updates, and fail2ban. It uses colour-coded output for readability and calculates compliance percentage. This enables rapid security posture verification without manual checking.

**Monitoring Script:**

The script connects remotely via SSH and collects system metrics including CPU usage, memory consumption, disk utilisation, network status, running processes, and security service status. It demonstrates professional remote administration practices. The script could be extended for alerting or logging functionality.

### 5.11 Integration with Week 4

Week 5 controls build upon the Week 4 foundation:

**Layered Security:**
- Week 4: SSH hardening prevents unauthorised access
- Week 5: Fail2ban adds additional protection
- Week 4: Firewall restricts network access
- Week 5: AppArmor restricts compromised process capabilities

**Defence in Depth:**

Multiple security layers provide redundancy. If one control fails, others remain effective. For example:
- SSH password authentication disabled (Week 4)
- Fail2ban monitors for brute force attempts (Week 5)
- Firewall restricts source IPs (Week 4)
- AppArmor limits SSH daemon capabilities (Week 5)

### 5.12 Next Steps

Week 6 will evaluate system performance and assess security control impact:

**Performance Testing:**
- Baseline system metrics
- CPU, memory, disk, network testing
- Application workload evaluation
- Security overhead assessment

Week 7 will conduct comprehensive security auditing:

**Security Audit:**
- Lynis security scanning
- Nmap port scanning
- Configuration compliance verification
- Security posture assessment

The security controls implemented in Weeks 4 and 5 provide a hardened baseline for the remaining coursework phases.
