## Script
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
## excute permission shell script
```
sudo chmod +x /usr/local/bin/tomcat_status.sh
```
sudo systemctl start tomcat.service
sudo /usr/local/bin/tomcat_status.sh
cat /var/lib/node_exporter/textfile_collector/tomcat_status.prom
```
## cronjob automatically
```
sudo crontab -e
```
```
*/1 * * * * /usr/local/bin/tomcat_status.sh
```


``
