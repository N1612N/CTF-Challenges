### Initial Scan
```sh
nmap 10.129.157.37 -F -v -oA Logs/Initial 
```
![[Pasted image 20240605190128.png | 700]]
### Port Scan
> [!Delayed]
> Took too much time to complete. Was not complete even after I completed the challenge.

### Aggressive Scan
```sh
nmap 10.129.157.37 -p 22,80 -A -T4 -v -oA Logs/AggressiveScan
```
![[Pasted image 20240605190038.png|700]]
