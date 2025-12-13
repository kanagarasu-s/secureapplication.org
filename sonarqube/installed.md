## sonarqube install steps
## install docker in centos 10
## Install required dependencies
```
sudo dnf install -y dnf-utils device-mapper-persistent-data lvm2
```
## Add the official Docker repository
```
sudo dnf config-manager --add-repo \
https://download.docker.com/linux/centos/docker-ce.repo
```
## Verify:
```
dnf repolist | grep docker
```
## Install Docker Engine
```
sudo dnf install -y docker-ce docker-ce-cli containerd.io
```
## Start and enable Docker
```
sudo systemctl start docker
sudo systemctl enable docker
```
## Check status:
```
systemctl status docker
```
## Verify Docker installation
```
docker --version
```
## Install Docker Compose (Plugin – Recommended)
```
sudo dnf install -y docker-compose-plugin
```
## Check:
```
docker compose version
```
## docker compose ymal file
```
sudo vi docker-compose.yml
```
```
version: "3"

services:
  sonardb:
    image: postgres:15
    container_name: sonardb
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: Sonar@123
      POSTGRES_DB: sonarqube
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: always

  sonarqube:
    image: sonarqube:lts-community
    container_name: sonarqube
    depends_on:
      - sonardb
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://sonardb:5432/sonarqube
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: Sonar@123
    ports:
      - "9000:9000"
    ulimits:
      nofile:
        soft: 65536
        hard: 65536
      nproc: 4096
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
    restart: always

volumes:
  postgres_data:
  sonarqube_data:
  sonarqube_extensions:
```
## Start services in background (detached)
```
docker compose up -d
```
## Stop containers
```
docker compose down
```
## Restart services
```
docker compose restart
```
## View running containers
```
docker compose ps
```
## View logs
```
docker compose logs
```
## Run command inside a service container
```
docker compose exec app bash
```
## sonarqube web console screen
```
http://192.168.55.160:9000/
```
## sonarqueb login
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/03bad1c7-c036-438f-bb11-7e1e53127673" />


