# Babyshop Docker

## Contents
1. [Prerequisites](#1-prerequisites)  
2. [Clone the Repository](#2-clone-the-repository)  
3. [Configure SSH Access](#3-configure-ssh-access)  
4. [Build the Docker Image](#4-build-the-docker-image)  
5. [Run the Docker Container](#5-run-the-docker-container)  
6. [Environment Variables](#6-environment-variables)  
7. [Database Migrations](#7-database-migrations)  
8. [Access the Application](#8-access-the-application)  
9. [Troubleshooting](#9-troubleshooting)  
10. [License](#10-license)  

---

## 1. Prerequisites

- A Linux host or VPS with Docker installed  
- A GitHub account with your SSH public key added  
- (Optional) Docker Compose for multi-container setups  

## 2. Clone the Repository

```bash
git clone git@github.com:DinefGH/babyshop.git
cd babyshop
```

## 3. Configure SSH Access
Make sure your SSH key is loaded and recognized by GitHub:

```bash
ssh-add ~/.ssh/id_ed25519
ssh -T git@github.com
# You should see: "Hi DinefGH! You've successfully authenticated..."
```


## 4. Build the Docker Image

```bash
docker build -t babyshop:latest .
```

-t babyshop:latest tags the image as babyshop:latest.


## 5. Run the Docker Container

```bash
docker run -d \
  --name babyshop-app \
  -p 8000:8000 \
  --restart unless-stopped \
  babyshop:latest
```

-d runs in the background

-p 8000:8000 maps container port 8000 → host port 8000

--restart unless-stopped auto-restarts on failure or reboot


## 6. Environment Variables

By default, settings.py uses:

```bash

DEBUG = True

ALLOWED_HOSTS = ['localhost', 'ip_v-server']
```


## 7. Database Migrations


Migrations run automatically at container start. To run manually:

```bash
docker exec -it babyshop-app python manage.py migrate
```

## 8. Access the Application
Open your browser to:

```bash
http://<YOUR_SERVER_IP>:8000/
```

## 9. Troubleshooting
DisallowedHost: Add your host/IP to ALLOWED_HOSTS and rebuild.

Permission denied (git clone): Work in a directory you own or chown it.

Container logs:

```bash
docker logs -f babyshop-app
```