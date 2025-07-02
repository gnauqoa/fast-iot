<div id="top">

<!-- HEADER STYLE: CONSOLE -->
<div align="center">

<h1 align="center">
  <br>
  <a href="http://www.amitmerchant.com/electron-markdownify"><img src="https://raw.githubusercontent.com/gnauqoa/fast-iot-fe/c8f4e737913bd522e44882d34bd66b855ba340f1/public/fast-iot-no-text.svg"alt="Markdownify" width="200"></a>
  <br>Fast IoT
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


<img src="https://img.shields.io/badge/Socket.io-010101.svg?style=flat-square&logo=socketdotio&logoColor=white" alt="Socket.io">
<img src="https://img.shields.io/badge/npm-CB3837.svg?style=flat-square&logo=npm&logoColor=white" alt="npm">
<img src="https://img.shields.io/badge/TypeORM-FE0803.svg?style=flat-square&logo=TypeORM&logoColor=white" alt="TypeORM">
<img src="https://img.shields.io/badge/Redis-FF4438.svg?style=flat-square&logo=Redis&logoColor=white" alt="Redis">
<img src="https://img.shields.io/badge/Mongoose-F04D35.svg?style=flat-square&logo=Mongoose&logoColor=white" alt="Mongoose">
<img src="https://img.shields.io/badge/Docker-2496ED.svg?style=flat-square&logo=Docker&logoColor=white" alt="Docker">
<img src="https://img.shields.io/badge/TypeScript-3178C6.svg?style=flat-square&logo=TypeScript&logoColor=white" alt="TypeScript">
<img src="https://img.shields.io/badge/MQTT-660066.svg?style=flat-square&logo=MQTT&logoColor=white" alt="MQTT">
<img src="https://img.shields.io/badge/Refine-24292F.svg?style=flat-square&logo=Refine&logoColor=white" alt="Refine" />
<img src="https://img.shields.io/badge/ESP32-000000.svg?style=flat-square&logo=espressif&logoColor=white" alt="ESP32">
<img src="https://img.shields.io/badge/ESP8266-002F6C.svg?style=flat-square&logo=wifi&logoColor=white" alt="ESP8266">
</div>
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

## Key Features

- 🧠 Backend API:
  Built with NestJS, provides REST & MQTT APIs with role-based authentication — ready for production.

- 🛠️ Admin Dashboard: Developed using Refine + Ant Design, enables real-time monitoring, device & user management — all in one place.

- 📡 Secure MQTT Server: Powered by Mosquitto, supports username/password authentication for secure, real-time communication with devices.

- 🔌 ESP32/8266 Library Included: Lightweight, custom C++ library for PlatformIO and Arduino IDE, featuring WiFiManager & MQTT helpers for instant setup.

- ⚡ Ultra-Fast Deployment: Get everything up and running in one command with Docker Compose — no manual config needed.

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

- 🌐 Backend API available at [`http://localhost:3000`](http://localhost:3000)
- 🛠️ Admin Dashboard available at [`http://localhost:4000`](http://localhost:4000)
- 📡 MQTT Broker for IoT device communication via port [`1883`](http://localhost:1883)
- 🧪 Swagger UI for dev mode at [`http://localhost:3000/docs`](http://localhost:3000/docs)

You can quickly connect your hardware using an ESP32 or ESP8266 board with our custom [PlatformIO/Arduino library](https://github.com/gnauqoa/fast-iot-pio.git) for PlatformIO and Arduino IDE

---

### 4. Development Tips

For deployment on a VPS with a custom domain, check out the step-by-step guide in [SETUP-VPS.md](./SETUP-VPS.md).

## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.
