# SSH setup

## Key-based authentication
### Client
```sh
ssh-keygen
ssh-copy-id user@server_ip
```
### Server
```sh
echo 'PasswordAuthentication no' >> /etc/ssh/sshd_config
service ssh restart
```

## Connection via SSH
```sh
ssh user@server_ip
```

## File transfer
```sh
scp /path/to/source user@server_ip:/path/to/destination
scp user@server_ip:/path/to/source /path/to/destination
```
### SSHFS (SSH File System)
```sh
apt install sshfs
```
```sh
sshfs user@server_ip:/path/to/source /path/to/destination
```
