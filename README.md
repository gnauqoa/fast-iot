<div align="center">

<h1>
  <img src="https://raw.githubusercontent.com/gnauqoa/fast-iot-fe/c8f4e737913bd522e44882d34bd66b855ba340f1/public/fast-iot-no-text.svg" width="150" alt="FastIoT logo" /><br />
  Fast IoT
</h1>

<h4>A full-stack framework to fast-track your IoT development.</h4>

<!-- Core Badges -->
<p align="center">
  <!-- Frontend -->
  <img src="https://img.shields.io/badge/Refine-242938.svg?style=flat-square&logo=data:image/svg+xml;base64,PHN2ZyBmaWxsPSIjZmZmIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMCIgaGVpZ2h0PSIyMCI+PHBhdGggZD0iTTAgMGgyMHYyMEgweiIgZmlsbD0ibm9uZSIvPjxwYXRoIGQ9Ik0xMi42NCA2LjI2TDYuMjYgMTIuNjRsMi4xMiAyLjEyTDE0Ljc2IDguMzh2Ni4xMmgyVjYuMjZoLTIuMTJ6Ii8+PC9zdmc+" alt="Refine" />
  <img src="https://img.shields.io/badge/NestJS-E0234E.svg?style=flat-square&logo=nestjs&logoColor=white" alt="NestJS" />
  <img src="https://img.shields.io/badge/ESP32%2F8266-3C3C3C.svg?style=flat-square&logo=espressif&logoColor=white" alt="ESP32/ESP8266" />
  <img src="https://img.shields.io/badge/PlatformIO-FF6600.svg?style=flat-square&logo=platformio&logoColor=white" alt="PlatformIO" />
</p>

<!-- TOC -->
<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#purpose-and-vision">Purpose & Vision</a> •
  <a href="#features">Features</a> •
  <a href="#repository-structure">Repository Structure</a> •
  <a href="#how-to-use">How To Use</a> •
  <a href="#license">License</a>
</p>

</div>


## Overview

FastIoT is an open-source platform designed to simplify and accelerate the development of full-stack IoT systems. This monorepo includes both the backend and admin dashboard, enabling seamless management, monitoring, and control of connected devices. It is built to support rapid prototyping, scalable deployment, and secure communication between IoT hardware and cloud infrastructure.

The backend, developed with NestJS and based on the nestjs-boilerplate, provides robust RESTful APIs, secure authentication, and efficient data processing. It integrates with PostgreSQL and MongoDB for flexible data storage, Redis for caching and real-time pub/sub, and Mosquitto as the MQTT broker for low-latency device communication.

The frontend, built with React and Refine, offers an intuitive admin interface to visualize device activity, manage users, and interact with IoT data in real time.

FastIoT is optimized for both local development and production deployment via Docker, enabling developers to launch the entire platform with a single command.

To support embedded devices, FastIoT includes an official C++ client library for **ESP8266** and **ESP32**, available at [fast-iot-pio](https://github.com/gnauqoa/fast-iot-pio).  
This library provides ready-to-use MQTT and HTTP wrappers, token-based authentication, OTA support, and auto-reconnect capabilities—allowing developers to connect their devices with minimal effort and maximum reliability.

## Purpose and Vision

- **Purpose**: FastIoT streamlines IoT project development by offering a comprehensive open-source platform that simplifies the integration of complex components such as servers, communication protocols, and admin interfaces. It enables developers, especially students and startups, to deploy complete IoT systems efficiently.
- **Vision**: To become the go-to open-source platform for IoT development, empowering developers—from individuals to small businesses—to transform ideas into reality quickly and effectively. FastIoT integrates advanced technologies like edge AI, ensuring flexibility, security, and future-readiness.

## 🚀 Features

### 🧠 Robust Backend API (NestJS)
- Built with **NestJS**, providing REST and MQTT APIs
- Secure **role-based authentication** with JWT
- Scalable architecture, production-ready out of the box

### 🛠️ Intuitive Admin Dashboard (Refine)
- Developed with **Refine** + **Ant Design**
- Real-time device monitoring & user management
- Built-in permissions and access control (RBAC)

### 📡 Secure MQTT Communication
- Integrated **Mosquitto** broker for low-latency messaging
- Supports **username/password authentication**
- Device-specific topics and payload schemas

### 🔌 Embedded Device SDK (ESP32/8266)
- Custom lightweight C++ library using **PlatformIO** or **Arduino IDE**
- Handles **WiFi setup**, **MQTT auth**, **reconnect logic**, and **OTA-ready**
- Supports **channel-based callbacks** and JSON messaging

### 📄 VPS Deployment Support
- Includes [step-by-step guide](./SETUP-VPS.md) to deploy the entire stack on a VPS
- Easily configure **custom domains** and **SSL certificates**
- Designed for scalable and secure production hosting

### ⚡ Instant Local Deployment (Docker)
- One-command launch with **Docker Compose**
- Includes backend, frontend, MQTT broker, Redis, and database services
- Supports both **local development** and **cloud deployment**

## Repository Structure

```
fast-iot/
├── fast-iot-be/     ← Submodule (backend)
├── fast-iot-fe/     ← Submodule (frontend)
├── fast-iot-pio/    ← Submodule (esp library)
├── docker-compose.yml
├── env-example
├── LICENSE
├── README.md
└── SETUP-VPS.md
```

---

## How To Use

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
cp ./fast-iot-be/env-docker-example ./fast-iot-be/.env
cp ./fast-iot-fe/env-example ./fast-iot-fe/.env
```

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

### 4. Development Tips

- 🚀 **VPS Deployment**: To deploy the entire stack (backend, frontend, MQTT broker) on a VPS with a custom domain, follow the step-by-step instructions in [SETUP-VPS.md](./SETUP-VPS.md).

- 🛠️ **Working with Submodules**: This project uses a monorepo structure with submodules for better separation and maintainability. Each submodule has its own `README.md` for setup and development instructions:
  - [`fast-iot-be`](https://github.com/gnauqoa/fast-iot-be/blob/main/README.md) — NestJS backend with REST API and MQTT integration
  - [`fast-iot-fe`](https://github.com/gnauqoa/fast-iot-fe/blob/main/README.md) — Refine-based admin dashboard for managing devices and users
  - [`fast-iot-pio`](https://github.com/gnauqoa/fast-iot-pio/blob/main/README.md) — C++ client library for ESP32/ESP8266 devices using PlatformIO or Arduino IDE


## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.
