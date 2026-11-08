# 🌶️ Chili Stress Detection System

### Smart IoT and AI-Based Chili Plant Monitoring System

Monitor leaf conditions and soil moisture, view pump status, and check the latest readings through a single web dashboard.

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-2.3-000000?style=for-the-badge&logo=flask&logoColor=white)
![Ultralytics](https://img.shields.io/badge/AI-YOLO-8A2BE2?style=for-the-badge)
![UiTM](https://img.shields.io/badge/UiTM-ITT569-8B1E3F?style=for-the-badge)

---

## 🌱 About the Project

This IoT project integrates **ESP32-CAM**, a soil moisture sensor, a **Flask** backend, and a **YOLO** detection model to help monitor chili plants.

The backend receives images and sensor readings, analyzes leaf conditions, manages pump logic, and provides data to the web dashboard.

> **Repository Scope:** This repository contains the backend and web dashboard code. ESP32 firmware is not included and must be configured separately to send data to the backend.

## ✨ Key Features

- 📷 Receive images from ESP32-CAM and display the latest camera feed on the dashboard.
- 🧠 Detect chili leaves using YOLO, including leaf count and estimated stressed leaves.
- 💧 Monitor soil moisture and enforce a maximum watering limit of **2 times per day**.
- 🌿 Manage fertilizer application logic with a **7-day cooldown period**.
- 📊 Display pump status, plant diagnosis, session statistics, and reading history.
- 🇲🇾 Use the Malaysia timezone (`Asia/Kuala_Lumpur`) for timestamps and daily calculations.
- 🐳 Include a `Dockerfile` to run the application inside a container.

## 🧩 System Overview

```text
ESP32-CAM ── Images + Soil Readings ──┐
                                     ├──> Flask + YOLO Model ──> Web Dashboard
ESP32 Sensor ── Soil Readings ───────┘             │
ESP32 Actuator <──────── Pump Status ──────────────┘
```

## 🗂️ Project Structure

| File | Description |
|---|---|
| `app.py` | Flask backend, image processing, sensor/pump logic, and API endpoints |
| `templates/dashboard.html` | Web monitoring dashboard |
| `best.pt` | YOLO model loaded by the backend |
| `yolov8n.pt` | Additional model file; not directly loaded by `app.py` |
| `plant_disease_model.h5` | Additional model file; not directly loaded by `app.py` |
| `requirements.txt` | Python dependencies |
| `Dockerfile` | Docker image configuration |
| `SETUP_GUIDE.md` | Additional system and hardware setup guide |

## 🚀 Getting Started

### Requirements

- Python **3.11** (matches the project's Docker image)
- `pip`
- Model file `best.pt` in the project root directory

### Run Locally

```powershell
# Create and activate a virtual environment (Windows)
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# Install dependencies
python -m pip install -r requirements.txt

# Run the backend and dashboard
python app.py
```

Open **http://localhost:5000** in your browser.

Check the backend at **http://localhost:5000/health**. A response containing `model_loaded: true` indicates that the YOLO model has been loaded successfully.

> Installing PyTorch and Ultralytics may take considerable time and disk space. If the model fails to load, check the terminal logs and ensure that `best.pt` exists.

### Run with Docker

```powershell
docker build -t chili-stress-detection .
docker run --rm -p 8080:8080 chili-stress-detection
```

Open **http://localhost:8080** in your browser. The container uses Gunicorn and port `8080`.

## 🔌 ESP32 Integration and API

The dashboard uses the same origin as the backend. Configure the ESP32 firmware to send requests to the correct backend address.

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Web dashboard |
| `GET` | `/health` | Backend status and model availability |
| `POST` | `/detect` | Upload raw images (`application/octet-stream`) from ESP32-CAM |
| `POST` | `/soil` | Submit soil sensor readings in JSON format |
| `GET` | `/pump-status` | Retrieve pump status and tracker information |
| `GET` | `/history` | Retrieve up to 20 latest records |
| `GET` | `/stats` | Retrieve session statistics |
| `GET` | `/latest-image-data` | Retrieve the latest image for the dashboard |

Example payload for `/soil`:

```json
{
  "soil_raw": 2500,
  "soil_percent": 45.0
}
```

The `/detect` endpoint also supports the optional headers `X-Soil-Raw` and `X-Soil-Percent`.

For model and tracker debugging, refer to the following endpoints:

- `/debug/model-info`
- `/debug/last-detections`
- `/debug/reset-trackers`

## ⚙️ Important Configuration

Thresholds and operational limits are configured in `app.py`:

- Soil is considered dry when moisture falls below **30%**.
- The water pump is limited to **2 activations per day**.
- Fertilizer application has a **7-day cooldown period**.
- Full YOLO analysis runs every **3 frames**, while the latest image is updated every frame.
- The model used is `best.pt` (`MODEL_PATH`).

Adjust these values according to your sensors, hardware, and model. Always test the pump logic safely before connecting an actual water pump.

> **Operational Note:** Reading history and pump trackers are stored in process memory rather than a database. This data resets whenever the backend restarts. Reset tracker endpoints are provided for testing purposes. Secure the backend before exposing it to a public network.

## 🛠️ Technologies Used

**Python · Flask · Ultralytics YOLO · OpenCV · NumPy · ESP32-CAM · HTML/CSS/JavaScript · Docker**

---

<div align="center">

Developed as a UiTM IoT Project · **ITT569**

</div>
