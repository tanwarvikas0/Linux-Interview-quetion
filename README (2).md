# Linux System Administration Interview Questions & Answers

A topic-wise collection of Linux system administration interview questions, organized by difficulty level (Beginner, Intermediate, Advanced).

## Table of Contents

1. [System Installation and Configuration](#topic-1-system-installation-and-configuration)
2. [System Monitoring and Performance Management](#topic-2-system-monitoring-and-performance-management)
3. [User and Group Management](#topic-3-user-and-group-management)
4. [Security Management](#topic-4-security-management)
5. [Automation and Scripting](#topic-5-automation-and-scripting)
6. [Software Installation and Management](#topic-6-software-installation-and-management)
7. [Log Management](#topic-7-log-management)
8. [Virtualization and Containerization](#topic-8-virtualization-and-containerization)
9. [Web Server Management](#topic-9-web-server-management)
10. [Advanced Nginx Management and Troubleshooting](#topic-10-advanced-nginx-management-and-troubleshooting)

---

## Topic 1: System Installation and Configuration

### Beginner Level


#### Q5. How do you change the hostname?
Use `sudo hostnamectl set-hostname server01` to change the system hostname.

### Intermediate Level

#### Q6. How do you create a new partition in Linux?
Use tools such as `fdisk` or `parted` to create partitions, then format and mount them as needed. Always verify the target disk before making changes.

#### Q7. What is the difference between ext4 and XFS?
Both are Linux file systems. ext4 is widely used for general-purpose systems, while XFS is common on enterprise systems and supports large files and file systems.

#### Q10. How do you configure a static IP address?
Ubuntu (Netplan):
sudo nano /etc/netplan/01-netcfg.yaml
sudo netplan apply


RHEL / Rocky Linux:
sudo nmcli connection modify "ens160" ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns "8.8.8.8"
sudo nmcli connection up "ens160"
The method depends on the distribution and network manager. On Ubuntu Server, Netplan YAML configuration is commonly used.

### Advanced Level

#### Q11. What is LVM?
LVM stands for Logical Volume Manager. It allows flexible management of storage by combining physical storage into volume groups and creating logical volumes.

#### Q12. How do you extend an LVM logical volume?
Extend the logical volume
If the volume group has at least 5 GB free:
sudo lvextend -L +5G /dev/myvg/mylv


To extend the logical volume and grow the filesystem automatically, use:
sudo lvextend -r -L +5G /dev/myvg/mylv


The -r option resizes the filesystem too, when supported.
Step 3: If the volume group has no free space
First, check whether a new disk is available:
lsblk


Example: A new disk is /dev/sdb. Confirm the disk name carefully; the following commands prepare it for LVM.
sudo pvcreate /dev/sdb
sudo vgextend myvg /dev/sdb
sudo vgs
Then extend the logical volume:
sudo lvextend -r -L +5G /dev/myvg/mylv
Step 4: Verify
sudo lvs
df -hT
lsblk


#### Q13. A server fails to boot. What will you do?
I will check console messages, boot logs, filesystem errors, and recent configuration changes. If necessary, I will use recovery mode to repair the problem.

#### Q14. How do you upgrade the Linux kernel safely?
I take a backup, check compatibility, and install the kernel update using the package manager. I reboot during a maintenance window, verify the kernel version and services, and keep the previous kernel for rollback.
Ubuntu:
sudo apt update
sudo apt upgrade
sudo reboot
uname -r




#### Q15. How do you troubleshoot a failed filesystem mount?
Check `lsblk`, `blkid`, `findmnt`, and system logs. Verify the UUID, filesystem type, mount point, and `/etc/fstab` configuration.

---

## Topic 2: System Monitoring and Performance Management

### Beginner Level


#### Q5. What is load average?
Load average shows the average number of tasks running or waiting for CPU or certain resources over 1, 5, and 15 minutes.

### Intermediate Level



#### Q9. How do you identify a process using high CPU?
Use `top` or `ps aux --sort=-%cpu | head`. Investigate the process and its workload before taking action.

#### Q10. What is swap memory?
Swap uses disk space as an extension of RAM when memory is under pressure. Heavy swap activity can reduce performance.

### Advanced Level

#### Q11. A server is slow. How will you troubleshoot it?
Check CPU, memory, disk I/O, load average, network performance, and logs. Identify the bottleneck before changing settings or restarting services.

#### Q12. What is I/O wait?
I/O wait is time when the CPU is idle while tasks wait for I/O operations to complete. High I/O wait can indicate slow storage or heavy disk activity.

#### Q13. How do you investigate a memory leak?
Track memory usage over time with tools such as `top`, `ps`, and application-specific metrics. Identify processes with continuously growing memory consumption.

#### Q14. How do you set up monitoring alerts?
Define thresholds for CPU, memory, disk, and service availability in the monitoring platform. Configure notifications and test that alerts reach the correct team.

#### Q15. How do you perform performance tuning?
Identify the bottleneck using metrics, change the relevant configuration, and compare performance before and after. Avoid tuning without evidence.

---

## Topic 3: User and Group Management

### Beginner Level

#### Q1. How do you create a user?
Use `sudo useradd -m username` to create a user with a home directory. Set the password with `sudo passwd username`.

#### Q2. How do you create a group?
Run `sudo groupadd developers` to create a group named `developers`.

#### Q3. How do you change a user's password?
Use `sudo passwd username`.

#### Q4. How do you check a user's groups?
Use `id username` or `groups username`.

#### Q5. What is the difference between chmod and chown?
`chmod` changes file permissions, while `chown` changes the file owner or group.

### Intermediate Level

#### Q6. How do you add a user to a group?
Use `sudo usermod -aG developers username`. The `-a` option preserves existing supplementary group memberships.

#### Q7. What does permission 755 mean?
The owner has read, write, and execute permissions. The group and others have read and execute permissions.

#### Q8. How do you lock a user account?
Use `sudo passwd -l username` to lock password authentication. Other authentication methods may need separate restrictions.

#### Q9. What is /etc/passwd?
It contains basic account information such as usernames, user IDs, home directories, and login shells.

#### Q10. What is /etc/shadow?
It stores password hashes and password-aging information, with restricted access.

### Advanced Level

#### Q11. What is the principle of least privilege?
Users should receive only the permissions necessary for their work. This reduces accidental damage and security risks.

#### Q12. What is the difference between SUID, SGID, and the sticky bit?
SUID runs an executable with the file owner's effective privileges, SGID has special group-related behavior, and the sticky bit restricts deletion of files in shared directories.

#### Q13. How do you troubleshoot permission denied errors?
Check ownership and permissions with `ls -l`, parent directory permissions, ACLs, and SELinux or AppArmor restrictions where applicable.

#### Q14. What is ACL in Linux?
Access Control Lists provide more detailed permissions for specific users and groups beyond standard owner, group, and others permissions.

#### Q15. How do you audit user access?
Review account and group memberships, sudo permissions, SSH keys, login records, and unused accounts. Remove unnecessary access according to policy.

---

## Topic 4: Security Management

### Beginner Level

#### Q1. What is a firewall?
A firewall controls network traffic according to rules. It helps prevent unauthorized network access.

#### Q2. What is SSH?
SSH is a secure protocol used to connect to and administer remote systems.

Q3. What is SELinux?
SELinux is a Linux security feature that controls what files and resources a process can access, even if normal permissions allow it.
Q4. What is AppArmor?
AppArmor is a Linux security feature that restricts what an application can access using security profiles.

#### Q5. Why are security patches important?
Security patches fix known vulnerabilities and bugs. Regular updates help protect systems from attacks.

### Intermediate Level

#### Q6. What is the difference between iptables and firewalld?
`iptables` is a firewall rules interface, while `firewalld` provides zone-based firewall management. Modern systems may use `nftables` underneath.

#### Q7. How do you check open ports?
Use `sudo ss -tulnp` to list listening TCP and UDP sockets and associated processes where permitted.

#### Q8. How do you secure SSH?
Use SSH keys, restrict access, disable direct root login where appropriate, and apply security updates. Test key access before disabling password authentication.

#### Q9. What is vulnerability assessment?
It identifies security weaknesses in systems, software, and configurations so they can be prioritized and fixed.

#### Q10. What is the principle of defense in depth?
It means using multiple security layers, including access control, firewalls, patching, monitoring, and backups.

### Advanced Level

#### Q11. A server is compromised. What will you do?
Follow the incident response process, isolate the server when appropriate, preserve logs and evidence, and notify the security team. Then investigate, remediate, and restore safely.

#### Q12. What is the difference between authentication and authorization?
Authentication verifies who a user is. Authorization determines what that user is allowed to access or do.

#### Q13. How do you investigate repeated failed SSH logins?
Review authentication logs, identify source addresses and patterns, and apply approved access restrictions. Check whether any account has been compromised.

#### Q14. What is SELinux enforcing mode?
Enforcing mode applies SELinux policies and blocks prohibited actions. Permissive mode logs policy violations without enforcing the restrictions.

#### Q15. How do you reduce the attack surface of a Linux server?
Disable unnecessary services and ports, remove unused software, restrict accounts and permissions, patch regularly, and monitor system activity.

---

## Topic 5: Automation and Scripting

### Beginner Level

#### Q1. What is shell scripting?
Shell scripting combines Linux commands in a file to automate repetitive tasks such as backups, checks, and maintenance.

#### Q2. What is Bash?
Bash is a command-line shell used to execute Linux commands and run shell scripts.

#### Q3. How do you run a shell script?
Use `chmod +x script.sh` and then `./script.sh`. Alternatively, run `bash script.sh`.

#### Q4. What is a cron job?
Cron schedules scripts and commands to run automatically at specified times.

#### Q5. What is a variable in Bash?
A variable stores a value that can be reused in a script, such as `NAME="server01"`.

### Intermediate Level

#### Q6. How do you schedule a script to run daily?
Use `crontab -e` and add `0 0 * * * /path/to/script.sh` to run it every day at midnight.

#### Q7. How do you check whether a script succeeded?
Check `$?` immediately after the command. Exit code `0` normally means success.

#### Q8. What is Ansible?
Ansible automates server configuration, software installation, and application deployment using playbooks, usually written in YAML.

#### Q9. What is the difference between Ansible, Puppet, and Chef?
All automate configuration management. Ansible commonly uses agentless SSH, while Puppet and Chef typically use agent-based management.

#### Q10. How do you troubleshoot a Bash script?
Run `bash -x script.sh` to trace command execution. Check errors, variables, file paths, and permissions.

### Advanced Level

#### Q11. How do you automate daily backups?
Write a script to back up the required files or databases, validate the result, and log failures. Schedule it and regularly test restoration.

#### Q12. How do you handle errors in Bash?
Check command exit codes, use conditional statements, and log failures. `set -euo pipefail` can help, but scripts must be designed carefully for its behavior.

#### Q13. What is idempotency in Ansible?
An idempotent task can be run multiple times and still leave the system in the intended state without unnecessary changes.

#### Q14. How do you automate configuration across 100 servers?
Use Ansible inventory and playbooks to apply the required configuration consistently. Test on a small group first and then roll out in stages.

#### Q15. How do you securely store automation credentials?
Use a secrets manager or Ansible Vault rather than storing plain-text passwords in scripts or repositories. Restrict access and rotate credentials as required.

---

## Topic 6: Software Installation and Management

### Beginner Level

#### Q1. What is a package manager?
A package manager installs, updates, and removes software while handling dependencies.

#### Q2. What is the difference between APT and DNF?
APT is commonly used on Ubuntu and Debian. DNF is used on modern RHEL-based distributions.

#### Q3. How do you install Nginx?
- Ubuntu: `sudo apt install nginx`
- RHEL: `sudo dnf install nginx`

#### Q4. How do you check a service status?
Use `sudo systemctl status nginx` to see whether Nginx is running and check recent status information.

#### Q5. What is Apache?
Apache HTTP Server is web server software used to host websites and applications.

### Intermediate Level

#### Q6. How do you start and enable a service?
Use `sudo systemctl enable --now nginx` to start it now and enable it at boot.

#### Q7. What is a software dependency?
A dependency is another package or library required for an application to work correctly.

#### Q8. How do you check installed packages?
- Ubuntu: `dpkg -l`
- RHEL: `rpm -qa`

#### Q9. What is the difference between MySQL and PostgreSQL?
Both are relational database systems. PostgreSQL offers advanced SQL and extensibility features, while MySQL is widely used for web applications.

#### Q10. How do you troubleshoot a service that fails to start?
Check `systemctl status service_name` and `journalctl -u service_name`. Verify the configuration, permissions, ports, and dependencies.

### Advanced Level

#### Q11. What is a systemd unit?
A systemd unit defines a resource managed by systemd, such as a service, mount, socket, or timer.

#### Q12. How do you troubleshoot package dependency conflicts?
Review the package manager's error messages and repository configuration, then identify incompatible versions or missing dependencies. Avoid forcing installation without understanding the impact.

#### Q13. What is a repository?
A repository is a source of software packages and metadata used by a package manager to install and update software.

#### Q14. How do you safely upgrade a production server?
Review changes and compatibility, back up important data, test in staging, and schedule a maintenance window. Upgrade in a controlled manner and verify services afterward.

#### Q15. How do you troubleshoot an application that fails after an update?
Review application and system logs, check dependency and configuration changes, and verify compatibility. Roll back using the approved procedure if necessary.

---

## Topic 7: Log Management

### Beginner Level

#### Q1. What are system logs?
System logs record events, warnings, errors, and service activity. They help administrators monitor and troubleshoot systems.

#### Q2. Where are Linux logs stored?
Many traditional log files are stored in `/var/log`. Locations vary by distribution and service.

#### Q3. What is journalctl?
It is a command used to view and filter logs collected by systemd's journal.

#### Q4. How do you check the latest logs?
Use `journalctl -n 100` to display the latest 100 journal entries.

#### Q5. How do you monitor a log file live?
Use `tail -f /path/to/logfile` to display new lines as they are written.

### Intermediate Level

#### Q6. How do you check logs for a specific service?
Use `journalctl -u nginx` to view journal entries for the Nginx service.

#### Q7. What is syslog?
Syslog is a standard for logging system messages. Logging services can collect and forward messages locally or to remote systems.

#### Q8. What is log rotation?
Log rotation archives, compresses, and removes old log files according to a retention policy.

#### Q9. What is the ELK Stack?
Elasticsearch, Logstash, and Kibana are used to collect, process, search, and visualize logs.

#### Q10. How do you search for errors in logs?
Use `grep -i "error" /path/to/logfile` to find lines containing the word "error," ignoring letter case.

### Advanced Level

#### Q11. A server's /var/log filesystem is full. What will you do?
Use `df -h` and `du` to find the largest files and directories. Check log rotation and safely archive or remove files according to retention requirements.

#### Q12. How do you centralize logs from multiple servers?
Configure a central logging platform and forward logs from each server. Secure transmission, access controls, and retention policies are important.

#### Q13. How do you identify a security incident through logs?
Look for unusual logins, repeated authentication failures, unexpected privilege changes, and suspicious processes. Correlate events across systems before drawing conclusions.

#### Q14. What is log retention?
Log retention defines how long logs are kept before archiving or deletion. It must balance troubleshooting needs, storage capacity, and compliance requirements.

#### Q15. How do you monitor logs automatically?
Use a log monitoring platform or scripts to detect specific error patterns and generate alerts. Test the alerts to avoid missed incidents and excessive false alarms.

---

## Topic 8: Virtualization and Containerization

### Beginner Level

#### Q1. What is virtualization?
Virtualization allows multiple virtual machines to run on a physical server, with each VM running its own operating system.

#### Q2. What is a hypervisor?
A hypervisor creates and manages virtual machines. Examples include KVM, VMware ESXi, and VirtualBox.

#### Q3. What is Docker?
Docker is a platform used to package and run applications in containers with their required dependencies.

#### Q4. What is a Docker image?
A Docker image is a template used to create containers.

#### Q5. What is a Docker container?
A container is an instance of an image that runs an application in an isolated environment.

### Intermediate Level

#### Q6. What is the difference between a VM and a container?
A VM runs a separate guest operating system, while a container shares the host kernel and isolates application processes.

#### Q7. How do you check running containers?
Use `docker ps` for running containers and `docker ps -a` for all containers.

#### Q8. How do you check Docker logs?
Use `docker logs container_name` to view container logs. Add `-f` to follow new log entries.

#### Q9. What is Kubernetes?
Kubernetes manages containerized applications across a cluster, including deployment, scaling, service discovery, and recovery.

#### Q10. What is OpenShift?
OpenShift is Red Hat's enterprise application platform built around Kubernetes, with additional tools for developers and operations teams.

### Advanced Level

#### Q11. What is container orchestration?
It automates the deployment, scaling, networking, and recovery of containers across a cluster.

#### Q12. A Docker container keeps restarting. How will you troubleshoot it?
Check `docker ps -a` and `docker logs container_name`. Investigate the application error, environment variables, health checks, and resource limits.

#### Q13. How do you persist data in Docker?
Use Docker volumes or bind mounts to store data outside the container's writable layer so it can survive container replacement.

#### Q14. What is a Kubernetes pod?
A pod is the smallest deployable unit in Kubernetes. It contains one or more containers that share network resources and can share storage.

#### Q15. How do you troubleshoot a Kubernetes pod that is not starting?
Use `kubectl describe pod POD_NAME` and `kubectl logs POD_NAME` to inspect events and logs. Check image pulls, configuration, resources, and scheduling constraints.

---

## Topic 9: Web Server Management

### Beginner Level

#### Q1. What is a web server?
A web server handles HTTP or HTTPS requests and serves websites, files, or application responses.

#### Q2. What is Nginx?
Nginx is a web server that can also act as a reverse proxy, load balancer, and caching server.

#### Q3. How do you install Nginx on Ubuntu?
Run `sudo apt update && sudo apt install nginx` to install Nginx from the configured package repositories.

#### Q4. How do you start Nginx?
Use `sudo systemctl enable --now nginx` to start the service and enable automatic startup at boot.

#### Q5. How do you test Nginx configuration?
Run `sudo nginx -t` to check the configuration syntax.

### Intermediate Level

#### Q6. Where is the Nginx configuration file?
The main file is usually `/etc/nginx/nginx.conf`. Site configurations may be stored in `sites-available`, `sites-enabled`, or `conf.d`.

#### Q7. What is a server block?
A server block configures how Nginx handles requests for a domain or IP address, including the document root or backend.

#### Q8. What is a reverse proxy?
A reverse proxy receives client requests and forwards them to backend application servers.

#### Q9. What is the difference between HTTP and HTTPS?
HTTPS uses TLS to encrypt network traffic and help authenticate the server. Plain HTTP does not provide TLS encryption.

#### Q10. What is an SSL/TLS certificate?
It supports server identity verification and enables encrypted HTTPS connections.

### Advanced Level

#### Q11. How do you configure multiple websites in Nginx?
Create separate server blocks with the appropriate `server_name`, document root or upstream, and TLS configuration. Test and reload Nginx after making changes.

#### Q12. What is load balancing in Nginx?
It distributes incoming requests across multiple backend servers to improve availability and distribute workload.

#### Q13. What is gzip compression?
Gzip reduces the size of supported HTTP responses, which can reduce bandwidth usage and improve load times.

#### Q14. What is caching in Nginx?
Caching stores eligible responses so repeated requests can be served faster, reducing backend workload.

#### Q15. How do you configure Let's Encrypt certificates?
Use a supported ACME client such as Certbot to obtain and configure a certificate. Verify automatic renewal and test it periodically.

---

## Topic 10: Advanced Nginx Management and Troubleshooting

### Beginner Level

#### Q1. How do you check whether Nginx is running?
Use `sudo systemctl status nginx` to check the service status.

#### Q2. How do you reload Nginx?
Run `sudo nginx -t` first, then `sudo systemctl reload nginx` if the configuration is valid.

#### Q3. Where are Nginx access and error logs stored?
Common locations are `/var/log/nginx/access.log` and `/var/log/nginx/error.log`, depending on the configuration.

#### Q4. What does HTTP 404 mean?
A 404 error means the requested resource was not found. Check the URL, document root, routing, and application configuration.

#### Q5. What does HTTP 403 mean?
A 403 error means access to the requested resource is forbidden. Check file permissions, access rules, and application authorization.

### Intermediate Level

#### Q6. What does HTTP 502 mean?
A 502 Bad Gateway error means Nginx received an invalid response from the upstream server or could not establish a valid upstream connection.

#### Q7. What does HTTP 504 mean?
A 504 Gateway Timeout error means the upstream server did not respond within the allowed time.

#### Q8. How do you troubleshoot a 502 error?
Check Nginx error logs, backend service status, upstream address and port, permissions, and connectivity between Nginx and the application.

#### Q9. How do you troubleshoot a 504 error?
Investigate backend response time, application and database performance, and network connectivity. Change timeouts only when the evidence supports it.

#### Q10. How do you check which process is using port 80?
Run `sudo ss -ltnp 'sport = :80'` to identify listening processes on port 80.

### Advanced Level

#### Q11. How do you optimize Nginx for high traffic?
Review worker processes, connection limits, keep-alive settings, caching, compression, and backend performance. Test changes under realistic workloads.

#### Q12. What is worker_processes?
It controls the number of Nginx worker processes. `auto` is a common starting point, but performance should be measured.

#### Q13. What is worker_connections?
It controls the maximum number of simultaneous connections each worker can handle, subject to operating system limits and other connection requirements.

#### Q14. How do you configure Nginx as a reverse proxy?
Use a `location` block with `proxy_pass` pointing to the backend. Configure appropriate forwarding headers and verify connectivity to the upstream.

#### Q15. A website is down, but Nginx is running. What will you do?
Check the Nginx error and access logs first, then confirm the request is reaching the right server block with `sudo nginx -T`. Verify the backend or upstream service is healthy and reachable, DNS resolves correctly, firewall rules allow the traffic, and the TLS certificate has not expired. Also check disk space, file permissions, and recent configuration or deployment changes. Fix the root cause before restarting or reloading services.
