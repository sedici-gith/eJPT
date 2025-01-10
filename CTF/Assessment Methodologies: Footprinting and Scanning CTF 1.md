## Assessment Methodologies: Footprinting and Scanning CTF 1

### Flag 1: The server proudly announces its identity in every response. Look closely; you might find something unusual

Scanning the target site with dirb, we found that the only accessible file is secrets.txt.
This file lists three folders
```
photos
secret-info
data
```
In the secret-info folder, there is a file containing flag 2.

We note the flag and proceed to identify the IP of the target site using whatweb which also provides additional information about the target.

Using whatweb on the target site, we find the first flag in the response.

### Flag 2: The gatekeeper's instructions often reveal what should remain unseen. Don't forget to read between the lines

We have found the flag during the previous analysis with the help of dirb.

### Flag 3: Anonymous access sometimes leads to forgotten treasures. Connect and explore the directory; you might stumble upon something valuable

We begin the analysis using nmap:
```
nmap IP_target -Pn -Ss
```
This reveals the following open ports:
```
22 - ssh
25 - smtp
80 - http
143 - imap
993 - imaps
3306 - mysql
```

Following the provided hints, we analyse ftp service with an nmap script.
First, locate FTP-realted scripts:
```
ls /usr/share/nmap/scripts -la | grep -e “ftp”
```
We proceed with ftp-anon:
```
nmap 192.148.53.3 -Pn -sS --script=ftp-anon
```
This reveals that the remote FTP folder is accessible and contains two files: creds.txt and flag.txt

We connect to the ftp service in anonymous mode and download flag.txt:
```
ftp ip_target (with anonymous)
```
```
get flag.txt
```

This file contains the third flag.

Additionally, downloading creds.txt, provides database credentials.

### Flag 4: A well-named database can be quite revealing. Peek at the configurations to discover the hidden treasure

The database is found on port 3306 (MySQL). We attempt to enumerate vulnerabilities using nmap scripts:
```
nmap 192.148.53.3 -Pn -sS --script=mysql-enum
```
The script indicates that the database accepts connections as the root user without a password. However, upon trying:
```
mysql -u root -p -h ip_target -P 3306
```
we find this is not possible.
Instead, we use the credentials found in creds.txt:
```
mysql -u db_admin -p -h ip_target -P 3306
```
After successfully connecting, we run the SQL command:
```
SHOW DATABASES;
```
This reveals the fourth and last flag needed.
