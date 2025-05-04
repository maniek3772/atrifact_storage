# JFrog Artifactory Setup with Docker (Debian 12)

This guide explains how to verify Docker installation, configure environment variables, set up database connection, and prepare security keys for running **JFrog Artifactory OSS** using Docker.

---

## 1 Check if Docker is installed

```bash
docker --version
```

- ***If Docker is installed***, you will see output like:
  ``` bash
  Docker version 28.1.1, build 4eba377
  ```
- ***If Docker is not installed***:
  ``` bash
  bash: docker: command not found
  ```

## 2 Check if Docker Compose is installed

***Old version (standalone binary)***
```bash
docker-compose --version
```

- ***If docker-compose is installed***, you will see output like:
  ``` bash
  Docker Compose version v2.35.1
  ```
- ***If docker-compose is not installed***:
  ``` bash
  bash: docker-compose: command not found
  ```

---
***New version (integrated with Docker CLI)***
```bash
docker compose version
```

- ***If docker compose is installed***, you will see output like:
  ``` bash
  Docker Compose version v2.35.1
  ```
- ***If docker compose is not installed***:
  ``` bash
  bash: docker compose: command not found
  ```

## 3 Verify Docker service status

```bash
sudo systemctl status docker
```

Docker should be ***active (running)***.

## 4 Set the $JFROG_HOME environment variable

```bash
export JFROG_HOME=/home/test/jfrog
```

## 5 Verify that $JFROG_HOME is available
```bash
echo $JFROG_HOME
```
Output should be:
```bash
/home/test/jfrog
```

## 6 Prepare configuration directories and files

```bash
mkdir -p $JFROG_HOME/artifactory/var/etc/
cd $JFROG_HOME/artifactory/var/etc/
touch ./system.yaml
sudo chown -R 1030:1030 $JFROG_HOME/artifactory/var
```

## 7 Configure database connection
Edit the system.yaml file:

```bash
nano $JFROG_HOME/artifactory/var/etc/system.yaml
```

Add the following content:

```yaml
shared:
  database:
    driver: org.postgresql.Driver
    type: postgresql
    url: jdbc:postgresql://postgres:5432/artifactorydb
    username: artifactory
    password: password
```

## 8 Create the security keys

Create etc/security directory
```bash
mkdir -p $JFROG_HOME/artifactory/var/etc/security
```

Generate master.key
```bash
openssl rand -hex 16 > $JFROG_HOME/artifactory/var/etc/security/master.key
```

Generate join.key
```bash
openssl rand -hex 16 > $JFROG_HOME/artifactory/var/etc/security/join.key
```

Set correct permissions
```bash
sudo chown -R 1030:1030 $JFROG_HOME/artifactory/var
```

## 9 Create docker compose file
```bash
nano $JFROG_HOME/docker-compose.yml
```

Add the following content:

```yaml
services:
  postgres:
    image: library/postgres:16.8
    container_name: postgres
    restart: always
    environment:
      POSTGRES_USER: artifactory
      POSTGRES_PASSWORD: password
      POSTGRES_DB: artifactorydb
    ports:
      - "5432:5432"
    networks:
      - artifactory-net

  artifactory:
    image: releases-docker.jfrog.io/jfrog/artifactory-oss:7.111.4
    container_name: artifactory
    restart: always
    ports:
      - "8081:8081"
      - "8082:8082"
    volumes:
      - ${JFROG_HOME}/artifactory/var/:/var/opt/jfrog/artifactory
    depends_on:
      - postgres
    networks:
      - artifactory-net

networks:
  artifactory-net:
    driver: bridge
```

## 10 First launch and login

***Run Artifactory***

Go to docker-compose.yml directory:
```bash
cd $JFROG_HOME/
```

Run docker compose file:
```bash
docker compose up
```

Wait for the message:

```arduino
artifactory  | ###############################################################
artifactory  | ###   All services started successfully in 67.622 seconds   ###
artifactory  | ###############################################################
```

***Access Artifactory Web UI***

```arduino
http://localhost:8082
```
or (if accessing from another machine)
```arduino
http://<server-ip>:8082
```

Default login credentials
```bash
Username: admin
Password: password
```
