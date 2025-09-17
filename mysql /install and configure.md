Installing MySQL on CentOS
==========================
## Download MySQL Repository
```
https://dev.mysql.com/downloads/repo/yum/
```
## Add the Software Repository
```
sudo wget https://dev.mysql.com/get/mysql84-community-release-el9-1.noarch.rpm
```
## Install MySQL
```
sudo yum install mysql-server -y
```
## Managing MySQL Service
```
sudo systemctl start mysqld
sudo systemctl enable mysqld
sudo systemctl status mysqld
```
## mysql service stop
```
sudo systemctl stop mysqld
```
## Find Temporary Password
```
sudo grep 'temporary password' /var/log/mysqld.log
```
## Configuring and Securing
```
sudo mysql_secure_installation
```
## Log into MySQL
```
mysql -u root -p
```
