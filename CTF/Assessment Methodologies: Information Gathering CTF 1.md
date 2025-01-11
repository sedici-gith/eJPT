## Assessment Methodologies: Information Gathering CTF 1

### Flag 1 - This tells search engines what to and what not to avoid

The file needed for search engines is robots.txt, located at http://target.ine.local/robots.txt.

Inside the file is hidden the first Flag.

***

### Flag 2 - What website is running on the target, and what is its version?

In order to discover the website CMS and its version, we need to use the whatweb command:
```
whatweb http://target.ine.local
```
The result of this command provides the second Flag.

***

### Flag 3 - Directory browsing might reveal where files are stored

During this reconnaissance phase, we can use dirb to enumerate folders on this targert:
```
dirb http://target.ine.local
```
Among all the folders, the one we are interested in is:
```
http://target.ine.local/wp-content/uploads/
```
In this folder we’ll find the third Flag

***

### Flag 4 - An overlooked backup file in the webroot can be problematic if it reveals sensitive configuration details

In order to find this flag, we need to search for a file that usually contains a backup or an old configuration of the site.

We can use gobuster with its arguments to search for a file with a particular extension under the webroot.
```
gobuster dir -u http://target.ine.local -w /usr/share/wordlists/dirb/common.txt -x zip,tar.gz,sql,bak,old -t 50 -b 404 -k 
```
- b 404 argument allows us exclude results with the status 404 not found.
- x is used to search files with names listed in the common list with specific extensions
- t is used to enable a faster searching
- k to ignore SSL or TLS errors

***

### Flag 5 - Certain files may reveal something interesting when mirrored

This is a bit tricky because there is no way to install or use HTTrack (even though the assignment lists it as one made available to use).

We need to use wget to download the webroot from the target site.
One of the files is an XML file that contains the last Flag.
