## Java 17.0.2 Installation Guide on CentOS
## Prerequisites
## Root or sudo access to the server
## Java openJDK 17.0.2 tar.gz file downloaded
## Create Java Installation Directory
```
sudo mkdir -p /usr/java
```
## Extract the Java Tar File
```
tar -xvf jdk-17.0.2_linux-x64_bin.tar.gz
```
## Set JAVA_HOME and PATH
```
sudo vi /etc/profile
```
## Add the following lines at the end of the file:
```
JAVA_HOME=/usr/java/jdk-17.0.2
PATH=$JAVA_HOME/bin:$PATH
```
## Apply the Changes 
```
source /etc/profile
```
## Verify Java Version

```
java -version
```
