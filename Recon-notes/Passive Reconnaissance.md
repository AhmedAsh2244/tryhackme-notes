# Reconnaissance - TryHackMe.

## Rooms Completed

### 1. Passive Reconnaissance
**Room:** https://tryhackme.com/room/passiverecon  
**Status:** ✅ Completed

**Key Concepts:**
- **WHOIS lookups:** `whois domain.com`
- **DNS enumeration:** `dig domain.com`, `nslookup domain.com`
- **Google Dorking:** `site:domain.com filetype:pdf`, `intitle:"index of"`
- **Shodan:** Searching for internet-exposed devices and services
- **TheHarvester:** Email and subdomain enumeration
- **OSINT Tools:** hunter.io, crt.sh, viewdns.info

**What I Learned:**
How to gather information about a target organization without making direct contact with their infrastructure. This is the initial phase of any penetration test and is crucial for building a target profile while remaining undetected.

---

### 2. Active Reconnaissance  
**Room:** https://tryhackme.com/room/activerecon  
**Status:** ✅ Completed

**Key Concepts:**
- **Ping sweeps:** `fping -a -g 10.10.0.0/24`
- **Port Scanning:** `nmap -sS -sV -O <TARGET_IP>`
- **Service enumeration:** `nmap -sC -sV -p- <TARGET_IP>`
- **Web enumeration:** `gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`
- **SMB enumeration:** `enum4linux -a <IP>`, `smbclient -L //<IP>/`

**What I Learned:**
The key difference between passive and active reconnaissance is that active recon involves sending packets directly to the target, which increases the risk of detection. Understanding when to use each technique and which tools are appropriate for different scenarios is essential for a stealthy and effective assessment.

---

## Next Steps
- Nmap Advanced
- Network Services
