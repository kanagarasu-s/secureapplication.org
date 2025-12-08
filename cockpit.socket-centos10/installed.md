## Change Cockpit Port 9090 to 7070
```
vi /usr/lib/systemd/system/cockpit.socket
```
```
[Unit]
Description=Cockpit Web Service Socket
Documentation=man:cockpit-ws(8)
Wants=cockpit-issue.service

[Socket]
ListenStream=7070
ExecStartPost=-/usr/share/cockpit/issue/update-issue '' localhost
ExecStartPost=-/bin/ln -snf active.issue /run/cockpit/issue
ExecStopPost=-/bin/ln -snf inactive.issue /run/cockpit/issue

[Install]
WantedBy=sockets.target
```
## Reload and Restart
```
sudo systemctl daemon-reload
sudo systemctl restart cockpit.socket
```

## Ensure Firewall Allows 7070

```
sudo firewall-cmd --add-port=7070/tcp --permanent
sudo firewall-cmd --reload
```
