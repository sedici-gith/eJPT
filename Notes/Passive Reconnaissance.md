## Passive Reconnaissance & Google Dorks

[eJPT - Exam Notes Index](https://github.com/sedici-gith/eJPT/tree/main)

### Site IP Discovery
```
whatis host
host target_host.it
```
It helps to discover IP address of the target host.
If we find 2 IPs it reveals the presence of a firewall/proxy such as cloudFlare

### Web Application discovery
```
whatweb target_host.it
```
This command analyzes the target and its technologies

Some other add-ons can be used to achieve the same goals:
* BuildWith
* Wappalyzer

Or a site like:
* Netcraft

### Site General Info
```
whois target_host
```
Retrieve information about domain registar.

### DNS Discovery
```
dnsrecon -d target_host
```
It is used to obtain all DNS records of the target.
Alternatively, dnsdumpster.com can be used.
```
nslookup 10.10.10.10 -type=txt 1.1.1.1
```
It is used to retrieve DNS TEXT records using cloudFlare Server
```
dig @1.1.1.1 10.10.10.10 MX
```
It is used to retrieve DNS MX records using cloudFlare Server

### Web Application Firewall (WAF)
```
wafw00f -a target_host
```
It is used to identify a WAF (Web Application Firewall) protecting the target.

### Subdomain Discovery
```
sublist3r -d target_host -e google
```
It is used to passively discover all sub-domains using google services.

### Site Cloning

HTTrack
```
sudo apt-get install webhttrack
```
It is used to clone target site to an offline folder.

### Search Crawlers
```
target_host.it/robots.txt
```
Files like robots.txt can be used to discover useful URIs containing information.
It is designed to restrict search engine crawlers from accessing certain URIs.

### Sitemap XML
```
sitemap.xml o sitemaps.xml o sitemap_index.xml
```
Files like these are useful to get a general idea of the structure of the target site.
These files are designed to communicate to search engine crawlers the site map.

### theHarvester - Mail finder
```
theHarvester -d hackersploit.it -b anubis,duckduckgo
```
It is used to “harvest” publicly available email addresses thanks to renowned search engines.

### Leaked Password Database

haveibeenpwned.com
All passwords or other leaked information can be found in this site.
It is used to discover target e-mail address or passwords leaked on various platforms.

## Google Dorks

* site:ine.com
* inurl:admin
* site:*.ine.com - subdomain enumeration
* intitle:admin
* filetype:pdf,doc,zip,etc.
* intitle:index of - directory listing
* cache:ine.com - site cached version
* inurl:auth_user_fil.txt
* inurl:passwd.txt

## Hacking database - exploit-db.com
