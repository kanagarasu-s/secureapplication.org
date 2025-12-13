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

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/08a584a1-bb96-442b-a23c-0b92f5603148" />

## sonarqube change password

## sonarqube homepage

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/7ddf34a4-8846-4e84-b1c8-11cab37629b7" />

## soanrqube create user token

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/984d306a-17b7-49da-b5b3-d2ed6a8aad98" />

## sonarqube click token

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/71939691-f0bc-48f0-93ae-f11dedd21684" />

## cope token

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/e1d3eccd-5cf3-400c-a47b-2f610712eaf0" />

## install plugin sonarqube and quilty in jenkins

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/9bbc9117-9cee-445a-bb06-72a2d3618d57" />

## add sonarqube token in jenkins

```
jenkins manage--> credentials
```

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/b2be33b0-89a9-43c8-93ea-c29c98afa33c" />

## configure sonarqube server

```
manage jenkins --> system
```
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/6255fcda-faf9-4280-a9d6-f14e25972fff" />

## configure sonarqube scanner
```
jenkins manager --> tools
```

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/4a18103d-e60e-4ef2-b923-0a0ad64892fe" />

## add jenkins pipeline

```
stage('Sonar scanner') {
            steps {
                withSonarQubeEnv('sonar-scanner') {
                  sh """ $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName-demo-security \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=demo-security """
                }
            }
        }
```


