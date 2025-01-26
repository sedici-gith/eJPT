## Host Discovery (Nmap basics)

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### Wireshark

Open Wireshark and use the command arp-scan or fping, then open the Wireshark tab Analyze -> Endpoints.
```
arp-scan -I eth0 -g 10.152.111.0/24
```
```
fping -I eth0 10.142.111.0/24 -a 2>/dev/null
```
* -a stands for IP alive.
* 2>/dev/null redirects all errors to a different location instead of the terminal.
* 0 - reachable hosts.
* 1 - some hosts are not reachable.
* 2 - no hosts are reachable.
* 3 - command line error.
* 4 -system call failure.
```
ping -c 1 -b 192.168.1.0
```
This command sends a ping to all hosts in a subnet.
-c 1 sends only 1 packet (-n 1 on Windows)
-b is used to send the packet in broadcast mode
```
netstat -antp
```
Lists all active TCP connections on Linux.
```
netdiscover -i eth0 -r 192.168.1.0/24
```
This tool is used to perform discovery checks on a subnet, printing out all MAC addresses using an ARP request.

***Using arp-scan and fping, we can identify which hosts might have ICMP protocol blocked by a firewall.***


### Nmap - Host Discovery
```
nmap -sn 192.168.1.* --send-ip
```
This performs a ping sweep to discover active hosts on the network.
* --send-ip is used to perform a fresh ARP request without using the cached one.
```
nmap -sn -PS 21,22,23,445,3389 10.10.10.1
```
This performs a TCP SYN Ping on the listed ports.
This scan sends a TCP SYN packet to listed ports, if the target host responds with a SYN ACK packet, the port is open. If the target responds with a RST packet, the port is closed. In either case, this behaviour identifies the host as alive.
```
nmap -sn -PA 21,22,23,445,3389 10.10.10.1
```
This performs a TCP ACK Ping on the listed ports.
This scan sends a TCP SYN packet to the listed ports. If the target host responds with a SYN-ACK packet, the port is open. If the target responds with an RST packet, the port is closed. In either case, this behaviour identifies the host as alive.

Examples useful for host discovery:
```
nmap -sn -v -T4 10.10.10.1
```
```
nmap -sn -PS 21,22,25,80,445,3389,8080 -T4 10.10.10.1
```
```
nmap -sn -PS 21,22,25,80,445,3389,8080 -PU 53,137,138,139 -T4 10.10.10.1
```
* -PP - ICMP Timestamp
* -PM - address mask queries
* -PE - ICMP echo request (ping)
* -PS - SYN Ping
* -PA - TCP ACK Ping
* -PU - UDP Ping
* -R - query DNS server also for offline hosts
* -n - no query to DNS server


### Nmap - Port Scanning
```
nmap -T4 -sS -sV --version-intensity 9 -O --osscan-guess -p- 10.10.10.1
```
This performs an aggressive scan against the target to identify the target OS and services running on open ports.
Version intensity ranges from 1 to 9 and specifies how hard Nmap should try to identify the service version.
```
nmap 10.0.0.1 -p 134 -sUV --script=discovery
```
This identifies the service on UDP port 134.
```
nmap 10.0.0.1 -sU --top-ports 25 --open
```
This scans the top 25 most commonly used ports on a system.


### Nmap scripting engine (NSE)
```
ls -al /usr/share/nmap/scripts/
```
This folder contains all Nmap scripts. 
```
ls -al /usr/share/nmap/scripts/ | grep -e “script_name”
```
Default script scan:
```
nmap -sS -sV -sC -p- -T4 10.10.10.1
```
Executes non-aggressive scripts on the target, without using known exploits.
```
nmap --script-help=mongodb-info
```
Retrieves information about a specific script and whether it is safe (no exploit).


### Firewall detection & IDS Evasion

-sA - ACK = 1
```
nmap -Pn -sA -p 445,3389 10.10.10.1
```
It is used to check if there is a firewall protecting the target. It sends a packet (-sA) with the flag ACK = 1. If no firewall is present, the response will be “unfiltered” on a specific port.

-f - packet fragmentation
```
nmap -Pn -sS -sV -F -f 10.10.10.1
```
Sends fragmented packets to obfuscate the intent of the scan.
Using the —mtu argument, it is possible to choose the length of the fragmented packets with an increment of 8 bytes.

-D - source IP spoofing
```
nmap -Pn -sS -sV -p 445,3389 -f --data-length 200 -D 10.10.10.254 10.10.10.1
```
--data-length appends random bytes to the packet payload.
-D obfuscates the sender’s IP. A specific IP can be spoofed, such as one authorized to communicate with the target. 

-g - source port spoofing
```
nmap -Pn -sS -sV -p445,3389 -f --data-length 200 -D 10.10.10.254 -g 53 10.10.10.1
```
-sN, -sX, sF - Null, XMAS e FIN

These send malformed packets to the target.


### Optimizing Nmap Scans

--host-timeout - maximum timeout for a single host
--scan-delay 5s - delay between packets.


### Nmap output formats

-oN text.txt - prints results to a text file.

-oX results.xml- produces an XML file for tools like Metasploit.

service postgresql start - start the database needed
msfconsole - launch Metasploit
* workspace -a pentest_1 - create a new workspace
* db_status - check DB status
* db_import nmap_xml.xml - imports Nmap XML file into the DB
* hosts - list imported hosts
* services - list imported services 
* db_map -Pn -sS -sV -O -p445 10.10.10.1 - perform an Nmap scan within Metasploit Framework and save the results to the database.
* workspace - lists available workspaces.

DBs created this way are persistent.
