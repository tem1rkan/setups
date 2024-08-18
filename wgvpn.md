# Wireguard (VPN) setup
## Server
```sh
umask 077 && mkdir -p /etc/wireguard/ && cd /etc/wireguard/ && wg genkey | tee priv | wg pubkey > pub
```
```ini
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = server_private_key
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE; ip6tables -A FORWARD -i %i -j ACCEPT; ip6tables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE; ip6tables -D FORWARD -i %i -j ACCEPT; ip6tables -t nat -D POSTROUTING -o ens3 -j MASQUERADE

[Peer 1]
AllowedIPs = 10.0.0.2/32
PublicKey = client_public_key
PresharedKey = preshared_key
```
```sh
systemctl enable --now wg-quick@wg0 && ufw allow 51820/udp && sysctl -w net.ipv4.ip_forward=1 && sysctl -p
```

## Client
```sh
umask 077 && mkdir -p /etc/wireguard/ && cd /etc/wireguard/ && wg genkey | tee priv | wg pubkey > pub
```
```ini
[Interface]
Address = 10.0.0.2/32
ListenPort = 51820
PrivateKey = client_private_key
DNS = 1.1.1.1

[Peer]
Endpoint = server_ip:51830
PublicKey = server_public_key
PresharedKey = preshared_key
AllowedIPs = 0.0.0.0/0
```
```sh
systemctl enable --now wg-quick@wg0
```
