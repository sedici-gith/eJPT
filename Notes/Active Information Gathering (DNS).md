## Active Information Gathering (DNS)

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### DNS Recon e DNS Zone Transfers

* A - resolves a hostname or domain to an IPv4 address
* AAAA - resolves a hostname or domain to an IPv6 address
* NS - reference to the domains name server
* MX - resolves a domain to a mail server
* CNAME - used for domain aliases
* TXT - text record
* HINFO - host information
* SOA - domain authority
* SRV - service records
* PTR - Resolves an IP address to a hostname

A DNS Zone Transfer not properly set up can be easily exploited.
```
dnsrecon -d zonetransfer.me
```
It is used to execute a DNS recon.
```
dnsenum -h
```
It is used to actively enumerate DNS.
```
cat /etc/hosts
```
In these files (location varies between operating systems), the local DNS resolver of the machine is contained.
```
dnsenum zonetrasfer.me
```
It is used to check if a DNS Zone Transfer is exploitable allowing the retrieval of DNS records that are usually not visible from the outside internal network.

If a DNS Zone Transfer exploit is not possibile, this command attempts a brute force attack on DNS records using a wordlist.

### DNS Brute Force
```
fierce -dns zonetrasnfer.me
```
It is used to attempts a brute force attack on the target domain searching for its DNS records.
