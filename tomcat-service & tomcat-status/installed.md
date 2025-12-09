## Systemd Service for Tomcat (root)
```
sudo vi /etc/systemd/system/tomcat.service
```
```
[Unit]
Description=Apache Tomcat Service
After=network.target

[Service]
Type=forking
User=root
Group=root

Environment="JAVA_HOME=/usr/java/jdk-17.0.2"
Environment="CATALINA_HOME=/usr/local/apache-tomcat-9.0.54"
Environment="CATALINA_BASE=/usr/local/apache-tomcat-9.0.54"
Environment="CATALINA_PID=/usr/local/apache-tomcat-9.0.54/temp/tomcat.pid"
Environment="JAVA_OPTS=-Djava.security.egd=file:/dev/./urandom"

ExecStart=/usr/local/apache-tomcat-9.0.54/bin/startup.sh
ExecStop=/usr/local/apache-tomcat-9.0.54/bin/shutdown.sh

Restart=on-failure
#RestartSec=10

[Install]
WantedBy=multi-user.target
```

## Reload systemd & Start Tomcat
```
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo systemctl status tomcat
```
## Allow 8080 Port
```
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```
## Create Apache Tomcat Status Script
```
sudo vi /usr/local/bin/tomcat_status.sh
```
```
#!/bin/bash

if systemctl is-active --quiet tomcat.service; then
  echo "tomcat_status 1" > /var/lib/node_exporter/textfile_collector/tomcat_status.prom
else
  echo "tomcat_status 0" > /var/lib/node_exporter/textfile_collector/tomcat_status.prom
fi
```
## Script Execution Permission
```
sudo chmod +x /usr/local/bin/tomcat_status.sh
```
```
sudo systemctl start tomcat.service
sudo /usr/local/bin/tomcat_status.sh
cat /var/lib/node_exporter/textfile_collector/tomcat_status.prom
```
## create the directory and set correct permissions
```
sudo mkdir -p /var/lib/node_exporter/textfile_collector
sudo chown root:root /var/lib/node_exporter/textfile_collector
```
## Then run your script again:
```
sudo /usr/local/bin/tomcat_status.sh
```
## Verify output exists:
```
cat /var/lib/node_exporter/textfile_collector/tomcat_status.prom
```
## Ensure Node Exporter is configured for textfile collector
```
sudo systemctl status node_exporter
```
```
sudo vi /etc/systemd/system/node_exporter.service
```
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=nodeusr
Group=nodeusr
Type=simple
ExecStart=/usr/local/bin/node_exporter \
 --collector.textfile.directory=/var/lib/node_exporter/textfile_collector

Restart=always

[Install]
WantedBy=multi-user.target
```
## service restart again:
```
sudo systemctl daemon-reload
sudo systemctl restart node_exporter
sudo systemctl status node_exporter
```

## cronjob for Auto Execution
```
sudo crontab -e
```
```
* * * * * /usr/local/bin/tomcat_status.sh
```
## Prometheus Alert Rule for Apache Tomcat
```
- alert: TomcatServiceDown
    expr: tomcat_status == 0
    for: 1m
    labels:
      severity: critical
    annotations:
      summary: "Tomcat Service DOWN"
      description: "Tomcat service is not running on instance: {{$labels.instance}}"
```

## many server start services tomcat
```
- alert: TomcatServiceDown
    expr: tomcat_status{instance=~"192.168.55.(51|47|52):9100"} == 0
    for: 1m
    labels:
      severity: critical
    annotations:
      summary: "Tomcat DOWN on {{ $labels.instance }}"
      description: "Tomcat service is not running on {{ $labels.instance }}. Please check the service immediately."
```
