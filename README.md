# Nmap Basic Port Scans - TryHackMe
Completed: 8 tasks - 120 points | Streak:2

## WHAT OI LEARNED
- TCP Connect vs SYN Scan
- UDP Scan is slow but important
- Icmp scan to cheak if host is up  (-Pn)
- Arp scan for local network
- Timing templates -T0 to -T5
  

## MY NOTES
sudo nmap -sS  10.10.x.x     SYN SCAN  (stealth)

sudo nmap -sU  10.10.x.x    UDP SCAN

sudo nmap -sT  10.10.x.x    TCP SCAN  (noisy)


## OPTIONS
-p- 	                  all ports (65535)

-p1-1023               	scan ports 1 to 1023

-F                    	100 most common ports

-r                     	scan ports in consecutive order

-T<0-5> 	               -T0 being the slowest and T5 the fastest

--max-rate 50 	          rate <= 50 packets/sec

--min-rate 15       	    rate >= 15 packets/sec

--min-parallelism 100 	   at least 100 probes in parallel
