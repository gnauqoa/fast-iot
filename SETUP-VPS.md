<div id="top">

<!-- HEADER STYLE: CONSOLE -->
<div align="center">

<h1 align="center">
  <br>
  <a href="http://www.amitmerchant.com/electron-markdownify"><img src="https://raw.githubusercontent.com/gnauqoa/fast-iot-fe/c8f4e737913bd522e44882d34bd66b855ba340f1/public/fast-iot-no-text.svg"alt="Markdownify" width="200"></a>
  <br>Fast IoT - VPS setup
  <br>
</h1>

<h4 align="center">A framework to quick start IoT project.</h4>
</div>
<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#purpose-and-vision">Purpose & Vision</a> •
  <a href="#key-features">Key Features</a> •
  <a href="#repository-structure">Repository Structure</a> •
  <a href="#how-to-use">How To Use</a> •
  <a href="#license">License</a>
</p>
<div align="center"> 
<img src="https://img.shields.io/github/license/gnauqoa/fast-iot-be?style=flat-square&logo=opensourceinitiative&logoColor=white&color=8a2be2" alt="license">
<img src="https://img.shields.io/github/last-commit/gnauqoa/fast-iot-be?style=flat-square&logo=git&logoColor=white&color=8a2be2" alt="last-commit">
<img src="https://img.shields.io/github/languages/top/gnauqoa/fast-iot-be?style=flat-square&color=8a2be2" alt="repo-top-language">
<img src="https://img.shields.io/github/languages/count/gnauqoa/fast-iot-be?style=flat-square&color=8a2be2" alt="repo-language-count">
</div>

<em>Built with the tools and technologies:</em>

<img src="https://img.shields.io/badge/Socket.io-010101.svg?style=flat-square&logo=socketdotio&logoColor=white" alt="Socket.io">
<img src="https://img.shields.io/badge/npm-CB3837.svg?style=flat-square&logo=npm&logoColor=white" alt="npm">
<img src="https://img.shields.io/badge/TypeORM-FE0803.svg?style=flat-square&logo=TypeORM&logoColor=white" alt="TypeORM">
<img src="https://img.shields.io/badge/Redis-FF4438.svg?style=flat-square&logo=Redis&logoColor=white" alt="Redis">
<img src="https://img.shields.io/badge/Mongoose-F04D35.svg?style=flat-square&logo=Mongoose&logoColor=white" alt="Mongoose">
<img src="https://img.shields.io/badge/Prettier-F7B93E.svg?style=flat-square&logo=Prettier&logoColor=black" alt="Prettier">
<img src="https://img.shields.io/badge/.ENV-ECD53F.svg?style=flat-square&logo=dotenv&logoColor=black" alt=".ENV">
<img src="https://img.shields.io/badge/Docker-2496ED.svg?style=flat-square&logo=Docker&logoColor=white" alt="Docker">
<img src="https://img.shields.io/badge/TypeScript-3178C6.svg?style=flat-square&logo=TypeScript&logoColor=white" alt="TypeScript">
<img src="https://img.shields.io/badge/MQTT-660066.svg?style=flat-square&logo=MQTT&logoColor=white" alt="MQTT">
<img src="https://img.shields.io/badge/Refine-24292F.svg?style=flat-square&logo=Refine&logoColor=white" alt="Refine" />

</div>

This guide will walk you through setting up a complete Node.js application on a VPS with SSL encryption using Nginx as a reverse proxy. The setup supports both domain-based and IP-based SSL configurations.

## Prerequisites

* Ubuntu VPS (20.04 LTS or later recommended)
* Root or sudo access
* Domain name (optional, for domain-based SSL)
* Basic knowledge of Linux command line

## Table of Contents

1. [Initial VPS Setup](#initial-vps-setup)
2. [SSL Setup - Option A: With Domain Name](#ssl-setup---option-a-with-domain-name)
3. [Application Deployment](#application-deployment)
4. [Firewall Configuration](#firewall-configuration)

## Initial VPS Setup

### Step 1: Update System and Install Dependencies

```bash
sudo apt update

# Install Fish shell (optional but recommended)
sudo apt install fish -y

sudo apt install vim
```

### Step 2: Install Docker

Docker will be used to containerize your application:

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

apt-cache policy docker-ce

sudo apt install docker-ce

sudo systemctl start docker
sudo systemctl enable docker

docker --version
```

## SSL Setup

Choose this option if you have a domain name pointing to your VPS.

### DNS Record Setup

Before proceeding with Nginx setup, make sure your domain DNS records are correctly configured to point to your VPS IP address.

| Type | Host           | Value (Your VPS IP) | TTL  |
| ---- | -------------- | ------------------- | ---- |
| A    | domain.example | YOUR\_VPS\_IP       | 3600 |
| A    | api            | YOUR\_VPS\_IP       | 3600 |
| A    | www            | YOUR\_VPS\_IP       | 3600 |

Replace `YOUR_VPS_IP` with the actual public IP address of your VPS.

### Step 1: Install Nginx

```bash
# Install Nginx
sudo apt install nginx -y

# Start and enable Nginx
sudo systemctl start nginx
sudo systemctl enable nginx

# Check Nginx status
sudo systemctl status nginx
```

### Step 2: Install Certbot for Let's Encrypt SSL

```bash
# Install Certbot and Nginx plugin
sudo apt install certbot python3-certbot-nginx -y

# Verify Certbot installation
certbot --version
```

### Step 3: Obtain SSL Certificate

Replace `DOMAIN_NAME` with your actual domain:

```bash
# Obtain SSL certificate for your domain
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Verify certificates
sudo certbot certificates
```

### Step 4: Configure Nginx for Frontend

#### Frontend Configuration (Port 4000)

```bash
sudo vim /etc/nginx/sites-available/domain.example
```

```nginx
server {
    listen 80;
    server_name domain.example www.domain.example;

    location / {
        proxy_pass http://localhost:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

server {
    listen 80;
    server_name api.domain.example;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

```

### Step 5: Enable Sites and Reload Nginx

```bash
sudo ln -s /etc/nginx/sites-available/domain.example /etc/nginx/sites-enabled/

sudo nginx -t
sudo systemctl reload nginx
```

### Step 6: Issue SSL Certificates

```bash
sudo certbot --nginx -d domain.example -d www.domain.example
sudo certbot --nginx -d api.domain.example
```

## Application Deployment

### 1. Clone the monorepo with submodules

```bash
git clone --recurse-submodules https://github.com/gnauqoa/fast-iot.git
cd fast-iot
```

> If you already cloned without `--recurse-submodules`, run:
>
> ```bash
> git submodule update --init --recursive
> ```

---

### 2. Create `.env` files from examples

Each app has its own `env-example`. Copy them like this:

```bash
cp env-example .env
```

Then edit `.env` in each folder to match your development environment.

---

### 3. Run all services

Make sure Docker is running, then:

```bash
docker compose up --build -d
```

This will spin up:

- Backend API at [`http://localhost:3000`](http://localhost:3000)
- Admin dashboard at [`http://localhost:4000`](http://localhost:4000)

## Firewall Configuration

Configure UFW (Uncomplicated Firewall) to secure your VPS:

```bash
# Allow SSH (important: do this first!)
sudo ufw allow OpenSSH

# Allow HTTP traffic
sudo ufw allow 80

# Allow HTTPS traffic
sudo ufw allow 443

# Allow MQTT port (optional)
sudo ufw allow 1883/tcp

# Enable firewall
sudo ufw enable

# Check firewall status
sudo ufw status
```

## Troubleshooting

### Certificate Renewal (Let's Encrypt only)

Let's Encrypt certificates auto-renew, but you can test renewal:

```bash
# Test certificate renewal
sudo certbot renew --dry-run

# Manual renewal if needed
sudo certbot renew
```

## Security Notes

- **Self-signed certificates** will show security warnings in browsers but still provide encryption
- **Let's Encrypt certificates** are trusted by all major browsers
- Always keep your system updated: `sudo apt update && sudo apt upgrade`
- Regularly check Docker container security updates
- Monitor your application logs for suspicious activity

## Additional Commands

### Useful Docker Commands

```bash
# View container logs
docker compose logs -f

# Restart services
docker compose restart

# Stop all services
docker compose down

# Update and rebuild
docker compose pull
docker compose up --build -d
```

### Useful Nginx Commands

```bash
# Test configuration
sudo nginx -t

# Reload configuration
sudo systemctl reload nginx

# Restart Nginx
sudo systemctl restart nginx

# Check configuration syntax
sudo nginx -T
```

This setup provides a robust, secure foundation for hosting your Node.js application with SSL encryption on a VPS.
