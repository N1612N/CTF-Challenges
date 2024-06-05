
```sh
nmap 10.129.157.37 -sC -sV -p 80 -v -oA Logs/80-HTTPScriptScan
```
![[Pasted image 20240605190756.png|700]]

```sh
searchsploit -t Apache 2.4.18 
```
![[Pasted image 20240605191553.png|700]]

![[Pasted image 20240605191705.png|700]]

![[Pasted image 20240605191745.png|700]]

Found a Directory from page source 

![[Pasted image 20240605192440.png|700x350]]
Searched `/nibbleblog` directory. found nothing much in there

```sh
gobuster dir -u http://10.129.157.37 -w /usr/share/wordlists/dirb/common.txt -o ./Logs/80-Gobuster
```
![[Pasted image 20240605192300.png|700]]

```sh
gobuster dir -u http://10.129.157.37/nibbleblog -w /usr/share/wordlists/dirb/common.txt -o ./Logs/80-Gobuster_nibbleblog
```

![[Pasted image 20240605192726.png|700]]

```url
http://10.129.157.37/nibbleblog/admin/
```
![[Pasted image 20240605193043.png|700]]
```url
http://10.129.157.37/nibbleblog/content/

http://10.129.157.37/nibbleblog/content/private/users.xml
http://10.129.157.37/nibbleblog/content/private/config.xml
```
![[Pasted image 20240605193242.png|700]]

![[Pasted image 20240605193314.png|700]]
![[Pasted image 20240605193406.png]]
Found some sensitive information like username
> [!Sensitive Information]
>username:  admin@nibbles.com
> mail id: noreply@10.10.10.134

```url
http://10.129.157.37/nibbleblog/languages/
```
![[Pasted image 20240605193604.png|700]]

```url
http://10.129.157.37/nibbleblog/plugins/
```
![[Pasted image 20240605193651.png|700]]```

```url
http://10.129.157.37/nibbleblog/themes/
```
![[Pasted image 20240605193840.png|700]]
```url
http://10.129.157.37/nibbleblog/README
```
![[Pasted image 20240605194021.png|700]]
> [! Sensitive Information]
> Nibbleblog Version v4.0.3
> Code Name: Coffee

```sh
searchsploit -t nibbleblog 4.0.3   
```
![[Pasted image 20240605195833.png|700]]

```url
http://10.129.157.37/nibbleblog/admin.php
```
![[Pasted image 20240605194204.png|700]]
> [! Sensitive information]
> Admin login page found

![[Pasted image 20240605194754.png|700]]
Blacklist protection enabled for user bruteforce
max 5 failed login attempts

Added `X-Forwarded-By: 127.0.0.1` in request header to bypass waf.
![[Pasted image 20240605195517.png]]
> [!Credential]
> admin // nibbles

```url
https://www.exploit-db.com/exploits/38489
```
![[Pasted image 20240605200526.png|700]]

```sh
https://www.rapid7.com/db/modules/exploit/multi/http/nibbleblog_file_upload/
```
![[Pasted image 20240605200614.png]]