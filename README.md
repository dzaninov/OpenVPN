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

## Find 3 VPN servers with lowest latency

Run these commands:
```
xargs -L 5 fping -a -c 3 < namecheap.srv 2> namecheap.ping
grep '3/3/0%' namecheap.ping | sort -t / -k 8 | head -3
```

## OpenVPN config file

Create namecheap.ovpn for Windows or namecheap.conf for Linux:
```
client
dev tun
proto udp
tun-mtu 1500
comp-lzo
persist-remote-ip
redirect-gateway

# Windows
# register-dns
# block-outside-dns

# Linux
script-security 2
up /etc/openvpn/update-resolv-conf
down /etc/openvpn/update-resolv-conf

auth SHA256
auth-nocache
auth-user-pass namecheap.auth
remote-cert-tls server
ca namecheap.crt
cipher AES-256-CBC
tls-cipher TLS-DHE-RSA-WITH-AES-256-CBC-SHA:TLS-DHE-DSS-WITH-AES-256-CBC-SHA:TLS-RSA-WITH-AES-256-CBC-SHA

remote vpn1.example.com
remote vpn2.example.com
remote vpn3.example.com
```
Place the namecheap.crt and namecheap.auth in the same folder where config file is.

Replace vpn?.example.com with chosen servers.

## Verify

Check your IP:
https://www.whatismyip.com/

Check for DNS leaks:
https://www.dnsleaktest.com/results.html

## Exceptions
Some content can only be accessed without VPN, reroute it to bypass the VPN.

1. Capture all outgoing connections:
```
tcpdump -n "tcp[tcpflags] & (tcp-syn) != 0 and src host 12.34.56.78" > capture.log
```
2. Try to access blocked content.

3. Get the list of used ip's.
```
awk '{print $5}' < capture.log | sort -u
```
4. Find a network for the ip address at https://www.arin.net.

5. Reroute all networks to bypass the VPN and test if you can now access the content.
```
ip route add 13.35.0.0/16 via 192.168.1.1
```
6. Delete routes one by one until content is blocked again to find which network needs to be rerouted.
```
ip route del 13.35.0.0/16
```

#### Amazon Prime
```
ip route add 13.35.0.0/16 via 192.168.1.1
```
