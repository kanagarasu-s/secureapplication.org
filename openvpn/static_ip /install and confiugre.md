openvpn configure static ip address with linux server
=====================================================

## Install OpenVPN
```
sudo yum install openvpn -y
```
## Create Client Directory and Copy Files
```
sudo mkdir -p /etc/openvpn/client
```
## Copy your configuration files:
```
sudo cp col5100-UDP4-1197-test24.ovpn /etc/openvpn/client/client.conf
sudo cp col5100-UDP4-1197-test24.p12 /etc/openvpn/client/
sudo cp col5100-UDP4-1197-test24-tls.key /etc/openvpn/client/
```
## Set proper file permissions:
```
sudo chmod 600 /etc/openvpn/client/client.conf
sudo chmod 600 /etc/openvpn/client/col5100-UDP4-1197-test24.p12
sudo chmod 600 /etc/openvpn/client/col5100-UDP4-1197-test24-tls.key
```
## Create Credentials File
```
sudo nano /etc/openvpn/client/credentials.txt
```
## Add the following content:
```
vpnusername
vpnpassword
```
## Secure the credentials file:
```
sudo chmod 600 /etc/openvpn/client/credentials.txt
```
## Update Client Configuration
```
sudo vi /etc/openvpn/client/client.conf
```
## Use the following content:
```
client
dev tun
proto udp
remote 96.11.189.98 1197 udp4
resolv-retry infinite
nobind
persist-key
persist-tun

pkcs12 /etc/openvpn/client/col5100-UDP4-1197-test24.p12
tls-auth /etc/openvpn/client/col5100-UDP4-1197-test24-tls.key 1
auth-user-pass /etc/openvpn/client/credentials.txt
remote-cert-tls server
verify-x509-name "wbrcol.ctleng.com" name

data-ciphers AES-256-GCM:AES-128-GCM:CHACHA20-POLY1305:AES-128-CBC
data-ciphers-fallback AES-128-CBC
auth SHA256
explicit-exit-notify
verb 3
```
## Enable and Start OpenVPN Client
```
sudo systemctl daemon-reload
sudo systemctl enable openvpn-client@client
sudo systemctl start openvpn-client@client
sudo systemctl status openvpn-client@client
```
## Verify VPN Interface
```
ip addr show tun0
```
## you go to pfsense Verify Common Name (CN)
```
CN=test24
```
## Verify Tunnel Network on pfSense
```
Go to VPN → OpenVPN → Servers → Edit your server.

Ensure the IPv4 Tunnel Network includes the desired static IP, e.g., 10.60.1.0/24.

The Client Specific Override IP (e.g., 10.60.1.8) must be inside this network.
```
## Restart OpenVPN Client
```
sudo systemctl restart openvpn-client@client
sudo systemctl status openvpn-client@client
```
## Check Logs
```
sudo journalctl -u openvpn-client@client -f
```



