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


