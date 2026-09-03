# CentOS CTF – Penetration Testing & Vulnerability Assessment
```
![Platform](https://img.shields.io/badge/Platform-CentOS-red)
![Testing](https://img.shields.io/badge/Testing-Penetration%20Testing-blue)
![Environment](https://img.shields.io/badge/Environment-CTF%20%2F%20Lab-green)
![Status](https://img.shields.io/badge/Status-Completed-success)
````
## Overview

This project documents a hands-on penetration testing and vulnerability assessment performed against a CentOS 6.7 virtual machine as part of the 12th Capture The Flag (CTF) challenge.

The assessment followed a structured penetration testing methodology, including reconnaissance, service enumeration, web enumeration, vulnerability analysis, exploitation, shell access, and privilege escalation analysis.

The target was tested within an isolated and authorized CTF/laboratory environment.

## Objective

The main objectives of the assessment were to:

- Discover the target machine
- Enumerate exposed services
- Identify web application attack surfaces
- Discover sensitive information
- Identify security vulnerabilities
- Validate vulnerabilities through controlled exploitation
- Obtain shell access
- Perform local system enumeration
- Investigate potential privilege escalation vectors
- Capture the CTF flags

## Scope

### Target

- Operating System: CentOS 6.7
- Environment: Virtual Machine / CTF Lab
- Network: Isolated VM Network

### Testing Phases

1. Reconnaissance
2. Service Enumeration
3. Web Enumeration
4. Vulnerability Analysis
5. Initial Exploitation
6. Web Shell
7. Reverse Shell
8. Local Enumeration
9. Privilege Escalation Analysis

## Methodology
Reconnaissance
      ↓
Host Discovery
      ↓
Service Enumeration
      ↓
Web Enumeration
      ↓
Vulnerability Identification
      ↓
Initial Exploitation
      ↓
Shell Access
      ↓
Local Enumeration
      ↓
Privilege Escalation Analysis
      ↓
CTF Flag Capture

## 1. Reconnaissance

The first phase focused on discovering the target machine and identifying the network environment.

Network discovery was performed using:

* `ifconfig`
* `netdiscover`

The Kali attacker machine and the target CentOS machine were configured within the same isolated VM network.

## 2. Service Enumeration

Nmap was used to identify exposed services and determine the target's attack surface.

The scan identified the following open ports:

| Port     | Service | Version             |
| -------- | ------- | ------------------- |
| 21/tcp   | FTP     | vsftpd 2.2.2        |
| 23/tcp   | Telnet  | Linux telnetd       |
| 80/tcp   | HTTP    | Apache httpd 2.2.15 |
| 3306/tcp | MySQL   | MySQL 5.1.73        |

The target operating system was identified as Linux, specifically CentOS.

## 3. Web Enumeration

The web application was examined through the target's HTTP service.

The `robots.txt` file disclosed multiple directories:

* /backup
* /admin
* /sql
* /uploads
* /hidden
* /temp

The /backup/ directory exposed sensitive files including:

* `config.inc.php`
* `database.sql`

This demonstrated an information disclosure vulnerability caused by publicly accessible sensitive resources.

## 4. Sensitive Information Discovery

The exposed database backup contained application credentials and a CTF flag.

The discovered credentials were used within the authorized CTF environment to validate access to the application's administrative interface.

Sensitive credentials are intentionally not reproduced in this public repository.

## 5. Vulnerability Analysis

Several vulnerabilities and security weaknesses were identified during the assessment.

### Information Disclosure

Sensitive directories and application paths were exposed through web enumeration and `robots.txt`.

**CWE:** CWE-200

**Severity:** Medium

### Impact

An attacker could use the exposed paths to map the application structure and locate sensitive resources such as backups and administrative areas.

### Insecure File Upload

The administrative interface allowed PHP files to be uploaded.

**CWE:** CWE-434

**Severity:** Critical

The upload functionality did not sufficiently restrict dangerous file types, allowing uploaded PHP content to be processed by the web server.

This weakness enabled controlled remote command execution within the CTF environment.

## 6. Administrative Access

Credentials discovered from the exposed database backup were used to authenticate to the administrative panel.

The administrative interface provided a file upload functionality located within the web application's administrative area.

## 7. Web Shell

The file upload functionality was tested using a PHP command-execution script within the authorized CTF environment.

Successful execution demonstrated that uploaded PHP content could be processed by the server.

The shell operated under the Apache web server account.

## 8. System Enumeration

After obtaining command execution, local enumeration was performed.

The assessment confirmed:

* Current user context: `apache`
* Working directory within the web upload path
* Ability to inspect accessible system information
* Apache HTTP Server environment

System information and configuration files were examined to identify potential privilege escalation paths.

## 9. Reverse Shell

A reverse shell was established to obtain a more interactive shell from the target machine.

The connection was validated using:

bash
whoami
id
pwd

The resulting shell operated within the authorized CTF environment.

## 10. Local Enumeration & Privilege Escalation Analysis

After obtaining shell access, local enumeration was performed to identify potential privilege escalation vectors.

The investigation included:

* System users
* File and directory permissions
* Cron jobs
* SUID binaries
* Operating system version
* Kernel version
* Sudo permissions

The target was running an outdated Linux kernel:

```text
2.6.32-573.el6.x86_64
```

The assessment identified the outdated kernel as a potential privilege escalation vector associated with known vulnerabilities.

No exploitable custom SUID binaries or misconfigured sudo permissions were identified for the Apache user during the documented assessment.

## 11. Findings Summary

| Finding                | Reference             | Severity |
| ---------------------- | --------------------- | -------- |
| Information Disclosure | CWE-200               | Medium   |
| Insecure File Upload   | CWE-434               | Critical |
| Outdated Linux Kernel  | CVE-2016-5195 related | High     |

## 12. Tools Used

* Kali Linux
* Nmap
* Netdiscover
* Netcat
* Linux Command Line
* Apache HTTP Server
* MySQL
* Virtual Machines
* Web Enumeration Techniques

## 13. Evidence

Screenshots documenting the assessment are available in the [`screenshots`](./screenshots/) directory.

The complete penetration testing report is available in [`Penetration-Testing-Report.pdf`](/Penetration-Testing-Report.pdf).

## 14. Recommendations

### Information Disclosure

* Remove sensitive directories from public web access.
* Prevent directory listing.
* Do not expose backup files through the web server.
* Review `robots.txt` for accidental information disclosure.

### File Upload Security

* Restrict allowed file extensions.
* Validate MIME types server-side.
* Store uploaded files outside the web root when possible.
* Disable script execution within upload directories.
* Rename uploaded files and avoid trusting user-controlled filenames.
* Apply strict server-side validation.

### Credential Security

* Never store credentials inside publicly accessible backups.
* Use strong, unique passwords.
* Store credentials securely.
* Rotate credentials after exposure.

### System Security

* Keep the operating system and kernel updated.
* Remove unnecessary services.
* Apply least-privilege principles.
* Regularly perform vulnerability assessments.

## 15. Conclusion

This CTF exercise demonstrated a complete penetration testing workflow, from reconnaissance and enumeration to vulnerability discovery and controlled exploitation.

The assessment showed how seemingly simple security weaknesses, such as exposed backup files and insecure file upload functionality, can be chained together to obtain command execution within a vulnerable environment.

The project provided practical experience in network enumeration, web security testing, vulnerability analysis, exploitation, Linux enumeration, and penetration testing documentation.

## Disclaimer

This project was performed strictly within an authorized Capture The Flag and educational laboratory environment.

The techniques and findings documented here must only be used against systems for which explicit authorization has been provided.
