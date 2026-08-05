# Active Reconnaissance - TryHackMe

**Room:** https://tryhackme.com/room/activerecon  
**Difficulty:** Easy  
**Status:** ✅ Completed  
**Date:** Aug 2026  
**Path:** Jr Penetration Tester > Network Reconnaissance

---

## Overview
This room introduces active information gathering techniques where you directly interact with the target system. Unlike passive recon, active methods send packets to the target, which provides more detailed results but increases the risk of detection.

## Key Concepts & Tools Covered

| Tool / Concept | Purpose |
| --- | --- |
| **ping** | `ping <IP>` - Checks if a host is alive and measures response time. First step in host discovery. |
| **traceroute** | `traceroute <IP>` - Maps the path packets take to reach the target. Useful for identifying network topology and firewalls. |
| **telnet** | `telnet <IP> <PORT>` - Tests if a specific port is open and allows manual banner grabbing. |
| **netcat** | `nc -nv <IP> <PORT>` - "Swiss army knife" for port scanning, banner grabbing, and creating connections. |
| **Web Browser** | Using developer tools to inspect HTTP headers, source code, and robots.txt for information leaks. |

## What I Learned
The core difference between active and passive reconnaissance is **direct interaction**. With active recon, you're generating network traffic that can be logged by the target's IDS/IPS or firewall. 

Key takeaway: **Always have permission before running active scans.** These techniques are noisy by design and should only be used in authorized engagements or labs like TryHackMe.

Understanding how to manually verify a service with `telnet` or `netcat` is crucial before launching automated tools like Nmap, as it helps confirm findings and avoid false positives.

## Skills Gained
- Host discovery and enumeration
- Manual port and service verification
- Basic network troubleshooting and mapping
- Understanding network protocols: ICMP, TCP, HTTP

## Screenshot
![Active Recon Completion](./images/active-recon.png)

---
**Previous Room:** Passive Reconnaissance  
**Next Room:** Nmap
