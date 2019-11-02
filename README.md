# OpenVPN
Configure OpenVPN to work with Namecheap VPN

## Download CA certificate
Go to this link:
https://www.namecheap.com/support/knowledgebase/article.aspx/10167/2248/setup-namecheap-vpn-on-ddwrt-v3-router

Search for "CA Cert" and download it to a file called NameCheap.crt.

## Get the VPN credentials and server list
Go to this link:
https://www.namecheap.com/apps/dashboard

Click on "Namecheap VPN" App.
Get your username, password and the server list.

Store the username and password in a file called NameCheap.pw in this format
```
username
password
```

## Find the fastest VPN server
```
fping -a -A -c 3 server1 server2 server3... | sort -t / -k 8 | head
```
Pick the server at the top of the list.

## OpenVPN config file
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
auth-user-pass NameCheap.pw
remote-cert-tls server
ca NameCheap.crt
cipher AES-256-CBC
tls-cipher TLS-DHE-RSA-WITH-AES-256-CBC-SHA:TLS-DHE-DSS-WITH-AES-256-CBC-SHA:TLS-RSA-WITH-AES-256-CBC-SHA

remote 12.34.56.78
```
Place NameCheap.crt and NameCheap.pw in the same folder where config file is.
Replace 12.34.56.78 with the server name you want to use.
