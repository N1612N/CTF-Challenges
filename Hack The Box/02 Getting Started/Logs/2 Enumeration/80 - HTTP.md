# 80 - HTTP Enumeration

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
### Enumeration Checklist
***Server: Apache/2.4.41 (Ubuntu)***
- [ ] [CVE-2021-41773 -> Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)](https://www.exploit-db.com/exploits/50383)
```sh
searchsploit apache 2.4.41 
```
![[Pasted image 20240606083605.png]]

***http-title: Welcome to GetSimple! - gettingstarted***
- [ ] [CVE - 2022-41544 -> GetSimple CMS v3.3.16 - Remote Code Execution (RCE)](https://www.exploit-db.com/exploits/51475)
- [ ] [Metasploit -> GetSimpleCMS PHP File Upload Vulnerability](https://www.rapid7.com/db/modules/exploit/unix/webapp/get_simple_cms_upload_exec/)
- [ ] [CVE - # -# -> Getsimple CMS 3.3.10 - Arbitrary File Upload](https://www.exploit-db.com/exploits/40008)
- [ ] Metasploit -> 0  exploit/unix/webapp/get_simple_cms_upload_exec
- [ ] Metasploit -> 1  exploit/multi/http/getsimplecms_unauth_code_exec
![[Pasted image 20240606091615.png]]

**http supported methods: GET HEAD POST OPTIONS**
> [!Nothing Severe in here]

***The anti-clickjacking X-Frame-Options header is not present.***
> [!Nothing Severe in here]

***The X-Content-Type-Options header is not set.***
> [!Nothing Severe in here]

***/robots.txt: Entry for '/admin/'*** and ***/admin/: This might be interesting.***
- [ ] ![[Pasted image 20240606085738.png]]
- [ ] ![[Pasted image 20240606090000.png]]
***Apache/2.4.41 appears to be outdated***
> [!Nothing Severe in here]

***/sitemap.xml: This gives a nice listing of the site content.***
> [!Nothing Severe in here]

***/data/: Directory indexing found.***
***/readme.txt: This might be interesting.***
![[Pasted image 20240606090202.png]]
***/LICENSE.txt: License file found may identify site software.***
> [!Nothing Severe in here]

***/backups              (Status: 301) [Size: 314] [--> http://10.129.47.83/backups/]     ***
> [!Nothing Severe in here]

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
>API key found

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

/index.php            (Status: 200) [Size: 5485]    
>[!Sensitive Info]
>API key found

/plugins              (Status: 301) [Size: 314] [--> http://10.129.47.83/plugins/]           
>[!Sensitive Info]
>API key found

/theme                (Status: 301) [Size: 312] [--> http://10.129.47.83/theme/]      
>[!Sensitive Info]
>API key found
