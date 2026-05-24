# 🐟 Smart Aquaculture Monitoring System

Sistem monitoring dan kontrol peternakan ikan modern berbasis IoT dengan arsitektur microservices. Dirancang untuk mengelola multiple peternakan dengan multiple kolam, lengkap dengan monitoring sensor real-time, kontrol katup otomatis, penjadwalan, dan simulasi.

![Stack](https://img.shields.io/badge/Stack-IoT%20%2B%20MQTT%20%2B%20InfluxDB%20%2B%20PostgreSQL%20%2B%20Grafana-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Docker](https://img.shields.io/badge/Docker-Ready-blue)

---

## ✨ Fitur Utama

### 📊 Monitoring Real-time
- 🌡️ **Suhu Air** (Temperature)
- 📏 **Kedalaman Air** (Depth)
- 💧 **Dissolved Oxygen** (DO)
- 🌫️ **Kekeruhan** (Turbidity)
- ⚗️ **pH Air**

### 🎮 Kontrol Kolam
- **Manual Mode** - Buka/tutup katup dengan satu klik
- **Scheduled Mode** - Jadwal pengurasan otomatis (harian/mingguan)
- **Auto Mode** - Pengurasan otomatis saat sensor melebihi batas

### 🏗️ Manajemen Multi-Tenant
- Kelola banyak peternakan
- Kelola banyak kolam per peternakan
- Threshold sensor per-kolam

### 🧪 Simulasi
- Atur nilai sensor manual via slider
- Mode auto-send untuk testing berkelanjutan
- 6 preset skenario (Normal, Overheating, Low DO, dll)

### 📈 Visualisasi
- Dashboard React dengan tema "Ocean Depths"
- Grafana embedded untuk analitik mendalam
- Real-time charts dengan auto-refresh

---

## 🏗️ Arsitektur Sistem

```
┌─────────────┐
│   ESP32     │ ─┐
│  (Sensor)   │  │
└─────────────┘  │   ┌──────────────┐
                 ├──▶│   Mosquitto  │
┌─────────────┐  │   │     MQTT     │
│  Simulator  │ ─┘   └──────┬───────┘
│   (Web)     │             │
└─────────────┘             ▼
                     ┌──────────────┐
                     │   Telegraf   │
                     └──┬────────┬──┘
                        │        │
                        ▼        ▼
                ┌──────────┐  ┌──────────┐
                │ InfluxDB │  │ Postgres │
                │(TimeSeries│  │(Relational│
                └────┬─────┘  └────┬─────┘
                     │             │
                     ▼             ▼
              ┌──────────┐   ┌──────────┐
              │ Grafana  │   │ Backend  │
              │          │   │ Node.js  │
              └────┬─────┘   └────┬─────┘
                   │              │
                   └──────┬───────┘
                          ▼
                   ┌──────────────┐
                   │   Frontend   │
                   │    React     │
                   └──────────────┘
```

---

## 🚀 Quick Start

### Prasyarat
- Docker Desktop atau Docker Engine
- Docker Compose v2+
- 4GB RAM, 10GB storage

### Install dalam 3 Langkah

```bash
# 1. Generate password MQTT
docker run --rm -v "$(pwd)/mosquitto/config:/mosquitto/config" \
  eclipse-mosquitto:2.0 \
  mosquitto_passwd -U /mosquitto/config/passwd.txt

# 2. Build dan jalankan
docker compose up -d --build

# 3. Buka browser
# Web App: http://localhost:3000
# Grafana: http://localhost:3001 (admin/admin123)
```

📖 **Tutorial lengkap:** [docs/TUTORIAL-INSTALASI.md](docs/TUTORIAL-INSTALASI.md)

---

## 🛠️ Tech Stack

### Backend
- **Node.js 20** + **Express** - REST API
- **MQTT.js** - Komunikasi MQTT
- **node-postgres (pg)** - PostgreSQL client
- **influxdb-client** - InfluxDB v2 client
- **node-cron** - Penjadwalan

### Frontend
- **React 18** + **Vite** - UI framework
- **React Router** - Routing
- **Recharts** - Charts
- **Lucide React** - Icons
- **CSS Variables** - Theming

### Infrastructure
- **Eclipse Mosquitto 2.0** - MQTT broker
- **Telegraf 1.28** - Data collector
- **InfluxDB 2.7** - Time-series database
- **PostgreSQL 15** - Relational database
- **Grafana 10.2** - Visualization

### Hardware
- **ESP32** - Microcontroller dengan WiFi
- **PubSubClient** - Library MQTT untuk Arduino
- **ArduinoJson** - JSON parser

---

## 📁 Struktur Project

```
smart-aquaculture/
├── docker-compose.yml          # Orchestration semua service
├── README.md                   # Dokumentasi ini
│
├── backend/                    # Node.js API
│   ├── server.js              # Express server + MQTT handler
│   ├── package.json
│   └── Dockerfile
│
├── frontend/                   # React Web App
│   ├── src/
│   │   ├── App.jsx           # Routes + Layout
│   │   ├── index.css         # Theme "Ocean Depths"
│   │   ├── services/api.js   # API client
│   │   └── pages/
│   │       ├── Dashboard.jsx
│   │       ├── Farms.jsx
│   │       ├── FarmDetail.jsx
│   │       ├── PondDetail.jsx
│   │       ├── Simulation.jsx
│   │       └── GrafanaView.jsx
│   ├── Dockerfile
│   └── nginx.conf
│
├── database/
│   └── init.sql               # Schema PostgreSQL
│
├── esp32/
│   └── esp32_aquaculture.ino  # Kode Arduino untuk ESP32
│
├── mosquitto/
│   └── config/
│       ├── mosquitto.conf
│       └── passwd.txt
│
├── telegraf/
│   └── telegraf.conf          # Bridge MQTT → InfluxDB + Postgres
│
├── grafana/
│   ├── provisioning/
│   │   ├── datasources/
│   │   └── dashboards/
│   └── dashboards/
│       └── aquaculture.json
│
└── docs/
    └── TUTORIAL-INSTALASI.md  # Tutorial lengkap
```

---

## 🌐 Endpoint & Port

| Service | URL | Default Credentials |
|---------|-----|---------------------|
| Frontend | http://localhost:3000 | - |
| Grafana | http://localhost:3001 | admin / admin123 |
| Backend API | http://localhost:5000 | - |
| InfluxDB | http://localhost:8086 | admin / admin123456 |
| PostgreSQL | localhost:5432 | aquaculture / aquaculture123 |
| MQTT | localhost:1883 | aquaculture / aquaculture123 |

---

## 📡 MQTT Topics

```
aquaculture/{farm_id}/{pond_id}/sensors    # Data sensor (dari ESP32 → Server)
aquaculture/{farm_id}/{pond_id}/control    # Perintah kontrol (Server → ESP32)
aquaculture/{farm_id}/{pond_id}/status     # Status device (ESP32 → Server)
```

### Format Payload Sensor
```json
{
  "temperature": 27.5,
  "depth": 120,
  "dissolved_oxygen": 6.8,
  "turbidity": 15,
  "ph": 7.2,
  "timestamp": 1731234567890
}
```

### Format Perintah Kontrol
```json
{
  "command": "open_valve" | "close_valve",
  "source": "manual" | "schedule" | "auto"
}
```

---

## 🎨 Tema UI

Project ini menggunakan tema **"Ocean Depths"** dengan dark mode profesional:

- **Background**: `#0a0e1a` (deep ocean)
- **Primary**: `#06b6d4` (cyan)
- **Accent**: `#3b82f6` (blue)
- **Fonts**: Outfit (display), Plus Jakarta Sans (body), JetBrains Mono (mono)

---

## 🔒 Default Threshold Sensor

| Sensor | Min | Max | Unit |
|--------|-----|-----|------|
| Suhu | 25 | 30 | °C |
| Kedalaman | 80 | 150 | cm |
| Dissolved Oxygen | 5 | 8 | mg/L |
| Kekeruhan | - | 50 | NTU |
| pH | 6.5 | 8.5 | - |

Threshold dapat diubah per-kolam di menu **Pengaturan**.

---

## 📜 Lisensi

MIT License - silakan gunakan, modifikasi, dan distribusikan untuk keperluan apapun.

---

## 🤝 Kontribusi

Project ini adalah template open. Anda bebas:
- Menambah fitur baru (notifikasi WhatsApp, alarm suara, dll)
- Mengganti sensor dengan yang real
- Modify UI sesuai brand
- Deploy ke cloud (AWS, GCP, DigitalOcean)

---

## 📞 Resources

- **Tutorial**: [docs/TUTORIAL-INSTALASI.md](docs/TUTORIAL-INSTALASI.md)
- **MQTT Docs**: https://mosquitto.org/documentation/
- **InfluxDB Flux**: https://docs.influxdata.com/flux/
- **Grafana**: https://grafana.com/docs/

---

Made with 💙 for the future of sustainable aquaculture
#   S m a r t - A q u a c u l t u r e - D a s h b o a r d  
 