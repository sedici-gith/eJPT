## Web Penetration Test (Basic)

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### Wordpress Enumeration
```
http://blog.thm/wp-json/wp/v2/users/
```
In this folder usually we can find something useful.

### WPSCAN
```
wpscan —url http://blog.htm —enumerate p
wpscan --url http://blog.thm --usernames kwheel --passwords /usr/share/wordlists/rockyou.txt
```
### NMAP
```
sudo nmap 127.0.0.1 -p 1234 -sS -sV --script=http-wordpress-enum --script-args http-wordpress-enum.search-limit=6000
```
### Hydra Web Brute Force
```
hydra -l <username> -P <wordlist> 10.10.92.234 http-post-form “/login:username=^USER^&password=^PASS^:/login” -V
```
"<path>:<login_credentials>:<invalid_response>"


### FFUF
```
ffuf -u http://10.10.229.115/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
```
You could also use any custom keyword instead of FUZZ, you just need to define it like this wordlist.txt:KEYWORD.
```
ffuf -u http://10.10.229.115/NORAJ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt:NORAJ
```
```
ffuf -u http://10.10.229.115/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e .php,.txt
```
```
-fc 403 - hide responde.code 403
-mc 200 - isolate response code 200
-fs 0 - filters out 0 byte file
-fr ‘/\..*’ - filter out files that begin with .
-x http://127.0.0.1:8080 - we can send all the traffic to BurpSuite
```
