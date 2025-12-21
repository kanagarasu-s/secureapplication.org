## Scenario
## Source server: CentOS (from where you connect)
## Target server: CentOS (to which you connect)
## User: centos (or root / any other user)
## Passwordless SSH works user-to-user, not server-to-server globally.

## Login to the Source CentOS server
```
ssh centos@SOURCE_SERVER_IP
```
## Generate SSH key pair (on Source server)
```
ssh-keygen -t rsa -b 4096
```
## When prompted:
## Enter file → Press Enter
## Enter passphrase → Press Enter (important for passwordless)
## Confirm passphrase → Press Enter
## This creates:
## ~/.ssh/id_rsa → private key
## ~/.ssh/id_rsa.pub → public key
## Copy public key to Target CentOS server
```
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@192.168.116.55
```
