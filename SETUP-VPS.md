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
  <a href="#prerequisites">Prerequisites</a> •
  <a href="#initial-vps-setup">Initial VPS Setup</a> •
  <a href="#ssl-setup">SSL Setup</a> •
  <a href="#application-deployment">Application Deployment</a> •
  <a href="#firewall-configuration">Firewall Configuration</a> •
  <a href="#troubleshooting">Troubleshooting</a> •
  <a href="#security-notes">Security Notes</a> •
  <a href="#additional-commands">Additional Commands</a>
</p>

</div>

This guide will walk you through setting up a complete Node.js application on a VPS with SSL encryption using Nginx as a reverse proxy. The setup supports both domain-based and IP-based SSL configurations.

## Prerequisites

* Ubuntu VPS (20.04 LTS or later recommended)
* Root or sudo access
* Domain name (optional, for domain-based SSL)
* Basic knowledge of Linux command line

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

### 3. Run all services

Then edit `.env` of each repo if need to match your development environment.
  
Start backend 

```bash
cd ./fast-iot-be
docker compose up --build -d
```

Start frontend

```bash
cd ../fast-iot-fe
docker ps --filter 'publish=4000' -q | xargs -r docker rm -f && docker build -t fast-iot-fe . && docker run -d -p 4000:4000 fast-iot-fe
```

This will spin up:

- 🌐 Backend API available at [`http://localhost:3000`](http://localhost:3000)
- 🛠️ Admin Dashboard available at [`http://localhost:4000`](http://localhost:4000)
- 📡 MQTT Broker for IoT device communication via port [`1883`](http://localhost:1883)
- 🧪 Swagger UI for dev mode at [`http://localhost:3000/docs`](http://localhost:3000/docs)

You can quickly connect your hardware using an ESP32 or ESP8266 board with our custom [PlatformIO/Arduino library](https://github.com/gnauqoa/fast-iot-pio.git) for PlatformIO and Arduino IDE

---

### 3. Run all services

Then edit `.env` of each repo if need to match your development environment.
  
```bash
# start backend
cd ./fast-iot-be
docker compose up --build -d

# start frontend
cd ../fast-iot-fe
npm run docker:start
```

This will spin up:

- 🌐 Backend API available at [`http://localhost:3000`](http://localhost:3000)
- 🛠️ Admin Dashboard available at [`http://localhost:4000`](http://localhost:4000)
- 📡 MQTT Broker for IoT device communication via port [`1883`](http://localhost:1883)
- 🧪 Swagger UI for dev mode at [`http://localhost:3000/docs`](http://localhost:3000/docs)

You can quickly connect your hardware using an ESP32 or ESP8266 board with our custom [PlatformIO/Arduino library](https://github.com/gnauqoa/fast-iot-pio.git) for PlatformIO and Arduino IDE

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
