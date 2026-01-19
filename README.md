*This project has been created as part of the 42 curriculum by akaarich.*

# Born2beRoot

## Description

Born2beroot is a system administration project that introduces virtualization concepts and Linux server configuration. 

The goal is to understand how operating systems work at a fundamental level, learning about machine organization and what happens behind the scenes during system operations.

This project covers virtual machine setup, partition management, user administration, and security implementation.

## Instructions

To evaluate this Born2beroot project, begin by establishing an SSH connection to the virtual machine using the command `ssh [your_login]@localhost -p 4242`. Once connected, systematically verify all project requirements:

#### Partitioning Scheme: 
Execute `lsblk` and `df -h` to confirm the implementation of the mandatory separate partitions using LVM with encryption. The system must display an encrypted LVM setup (`sda5_crypt`) containing logical volumes for: `/` (10G), `[SWAP]` (2.3G), `/home` (5G), `/var` (3G), `/srv` (3G), and `/tmp` (3G), with a separate unencrypted `/boot` partition of 500MB on `sda1`.

#### SSH Configuration: 
Use `sudo ss -tunlp | grep 4242` to verify the SSH daemon is listening exclusively on port 4242.

#### Password Policy: 
Validate the strict password policy by running `sudo chage -l [your_login]`. The output must show a maximum password age of 30 days (PASS_MAX_DAYS), a minimum age of 2 days (PASS_MIN_DAYS), and a warning period of 7 days (PASS_WARN_AGE). Cross-reference these settings in `/etc/login.defs`.

#### Firewall Security:
Inspect the firewall rules with `sudo ufw status verbose`. The configuration must explicitly allow TCP traffic on port 4242 while denying all other unnecessary ports.

#### Bonus Services:
For the bonus part, verify the installation and operational status of the additional services:

* **WordPress:** Check if the web service is accessible and configured.
* **MariaDB:** Confirm the database service is running with `sudo systemctl status mariadb`.
* **Lighttpd:** Verify the web server status with `sudo systemctl status lighttpd`.
* **Fail2ban:** Check that the intrusion prevention software is running with `sudo systemctl status fail2ban` and verify active jails with `sudo fail2ban-client status`.

#### Monitoring Script: 
Execute the monitoring script with `sudo bash monitoring.sh` to confirm its functionality. Verify its automatic execution by inspecting the cron jobs with `sudo crontab -l`, which should include an entry to run the script every 10 minutes.

## Project Description - OS Configuration

We chose Debian as our operating system for this project.

* **Pros of Debian:**
    * Excellent stability and reliability.
    * Massive software repository.
    * Strong community support and documentation.
    * Predictable release cycle.
    * Beginner-friendly for learning.

* **Cons of Debian:**
    * Packages can be older due to stability focus.
    * Less frequent updates than rolling releases.
    * Not as enterprise-aligned as RHEL-based systems.

* **Why Not Rocky Linux:**
    While Rocky offers enterprise compatibility, Debian's educational resources and community make it better for learning system administration fundamentals.

### => Debian vs Rocky Linux:
Debian offers excellent stability and beginner-friendly resources, making it ideal for learning. Rocky Linux, while great for enterprise compatibility, has a steeper learning curve and less beginner documentation. We selected Debian for its educational value.

### => AppArmor vs SELinux:
AppArmor provides simpler, path-based security that's easier to configure and understand for students. SELinux offers more granular control but requires complex configuration. We implemented AppArmor as it's more approachable for learning security fundamentals.

### => UFW vs firewalld:
UFW (Uncomplicated Firewall) was chosen for its simple command syntax and intuitive management. While firewalld offers dynamic zone-based firewall management, UFW's straightforward approach is better suited for learning basic firewall concepts.

### => VirtualBox vs UTM:
VirtualBox was selected because it works on all computer systems and has extensive documentation. UTM is optimized for Apple Silicon but has limited resources. VirtualBox's cross-platform compatibility makes it better for educational purposes.

## Main Design Choices

### Partitioning Strategy
- Implemented encrypted LVM with separate partitions for security and organization
- `/boot` unencrypted (500MB) for system boot
- Encrypted LVM containing:
  - `/` (root): 10G for system files
  - `/home`: 5G for user data
  - `/var`: 3G for variable data
  - `/srv`: 3G for service data
  - `/tmp`: 3G for temporary files
  - `[SWAP]`: 2.3 for virtual memory

### Security Implementation
- **Password Policy**: Strict rules enforced via `/etc/login.defs` and PAM
  - Minimum 10 characters with uppercase, lowercase, and number
  - Maximum 3 consecutive identical characters
  - Password expires every 30 days
  - Minimum 2 days between changes
  - 7-day warning before expiration
  - Password cannot contain username
  - For root: All rules apply except the "7 new characters" requirement
- **Firewall**: UFW configured to allow only port 4242 (SSH)
- **SSH Security**: Changed default port to 4242, disabled root login
- **AppArmor**: Enabled for mandatory access control
- **Sudo Security**: Limited to 3 attempts, TTY mode enabled, all actions logged
- **PASS i use**: PAssword123.

### User Management
- Created user `akaarich` (matching 42 login)
- Added user to both `user42` and `sudo` groups as required
- Implemented strict sudo policies:
  - Authentication limited to 3 attempts
  - Custom error messages for failed attempts
  - All sudo actions archived in `/var/log/sudo/`
  - Restricted PATH for security
  - TTY mode enabled
- Root access restricted, normal operations performed via sudo
- During evaluation: Can create additional users and assign to groups as needed

### Service Management
- Minimal installation approach
- SSH service on custom port 4242
- UFW firewall service enabled
- Monitoring script via cron every 3 minutes
- **(Bonus) Additional services:** WordPress, MariaDB, Lighttpd, **Fail2ban**

## Resources

### References & Documentation
####  => Primary Guidance:
* Collaborative support and knowledge sharing with 42 peers and colleagues.
* Debian Installation Guide: Official documentation for OS installation and partitioning.
* SSH Server Configuration: OpenSSH manual for port configuration and security settings.
* Online Tutorials: Various YouTube guides and web articles for practical implementation.
* System Administration Forums: Community discussions for troubleshooting and best practices.

####  => AI Assistance Disclosure:
* **Error Correction:** Identifying and correcting syntax errors in commands and configurations.
* **Writing Improvement:** Enhancing sentence structure and technical documentation clarity.
* **Command Guidance:** Providing useful command suggestions and configuration examples.
* **Troubleshooting Advice:** Offering solutions to common system administration challenges.
* **Learning Support:** Explaining complex concepts in simpler terms for better understanding.
