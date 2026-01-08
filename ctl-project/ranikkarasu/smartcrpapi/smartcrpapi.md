```
vi /etc/systemd/system/smartcrpapi.service
```

```
[Unit]
Description=SMARTCRP API Spring Boot Application
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/application

ExecStart=/usr/bin/java \
 -Dspring.profiles.active=local \
 -Dspring.config.location=file:/root/application/properties/apps.properties,classpath:application.properties \
 -jar /root/application/smartcrp-api-0.0.1.jar

Restart=always
RestartSec=10
SuccessExitStatus=143

StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```
