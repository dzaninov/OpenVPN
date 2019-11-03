# OpenVPN
Configure OpenVPN to work with Namecheap VPN on Linux and Windows.

## Download the CA certificate
Go to this link:
https://www.namecheap.com/support/knowledgebase/article.aspx/10167/2248/setup-namecheap-vpn-on-ddwrt-v3-router

Search for "CA Cert" and download it to a file called namecheap.crt.

## Get the VPN credentials and server list
Go to this link:
https://www.namecheap.com/apps/dashboard

Click on "Namecheap VPN" app.

Get your username, password and store them in a file named namecheap.auth having this format:
```
username
password
```

Copy paste the server list to a file named namecheap.srv.

## Find the fastest VPN server

Run these commands:
```
xargs -L 5 fping -a -c 3 < namecheap.srv 2> namecheap.ping
grep '3/3/0%' namecheap.ping | sort -t / -k 8 | head
```
Pick the server at the top of the list.

## OpenVPN config file

Create namecheap.ovpn
```
client
dev tun
proto udp
tun-mtu 1500
comp-lzo
persist-remote-ip
redirect-gateway

auth SHA256
auth-nocache
auth-user-pass namecheap.auth
remote-cert-tls server
ca namecheap.crt
cipher AES-256-CBC
tls-cipher TLS-DHE-RSA-WITH-AES-256-CBC-SHA:TLS-DHE-DSS-WITH-AES-256-CBC-SHA:TLS-RSA-WITH-AES-256-CBC-SHA

remote vpn.example.com
```
Place the namecheap.crt and namecheap.auth in the same folder where config file is.

Replace vpn.example.com with the server name you want to use.
