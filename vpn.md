# Wireguard (VPN) setup

## Server
```sh
# apt install wireguard-tools
umask 077 && wg genkey | tee /etc/wireguard/server_private_key | wg pubkey > /etc/wireguard/server_public_key
```
```ini
# /etc/wireguard/wg0.conf

[Interface]
PrivateKey = server_private_key
Address = 10.0.0.1/24
ListenPort = 51830

# use your internet interface instead of 'ens3' 
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE; ip6tables -A FORWARD -i wg0 -j ACCEPT; ip6tables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE; ip6tables -D FORWARD -i wg0 -j ACCEPT; ip6tables -t nat -D POSTROUTING -o ens3 -j MASQUERADE

[Peer]
PublicKey = client_public_key
AllowedIPs = 10.0.0.2/32

# add peers
# [Peer]
# PublicKey = client2_public_key
# AllowedIPs = 10.0.0.3/32
```
```sh
systemctl enable --now wg-quick@wg0 && ufw allow 51830/udp && sysctl -w net.ipv4.ip_forward=1 && sysctl -p
```

## Client
```sh
# apt install wireguard-tools
umask 077 && wg genkey | tee /etc/wireguard/client_private_key | wg pubkey | tee /etc/wireguard/client_public_key
```
```ini
# /etc/wireguard/wg0.conf

[Interface]
PrivateKey = client_private_key
ListenPort = 51830
Address = 10.0.0.2/32
DNS = 1.1.1.1

[Peer]
PublicKey = server_public_key
Endpoint = server_ip:51830
AllowedIPs = 0.0.0.0/0
```
```sh
systemctl enable --now wg-quick@wg0
```
