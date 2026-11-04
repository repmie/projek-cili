<div align="center">

# 🌶️ Chili Stress Detection System

### Sistem pemantauan cili pintar berasaskan IoT dan AI

Pantau keadaan daun dan kelembapan tanah, lihat status pam, dan semak bacaan terkini melalui satu dashboard web.

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-2.3-000000?style=for-the-badge&logo=flask&logoColor=white)
![Ultralytics](https://img.shields.io/badge/AI-YOLO-8A2BE2?style=for-the-badge)
![UiTM](https://img.shields.io/badge/UiTM-ITT569-8B1E3F?style=for-the-badge)

</div>

---

## 🌱 Tentang projek

Projek IoT ini menggabungkan **ESP32-CAM**, sensor kelembapan tanah, backend **Flask**, dan model pengesanan **YOLO** untuk membantu memantau tanaman cili. Backend menerima imej dan bacaan sensor, menganalisis daun, mengurus logik pam, dan membekalkan data kepada dashboard.

> **Skop repo:** Kod backend dan dashboard web. Firmware ESP32 tidak disertakan dalam repo ini dan perlu dikonfigurasi berasingan untuk menghantar data ke backend.

## ✨ Ciri utama

- 📷 Terima imej daripada ESP32-CAM dan paparkan suapan terkini pada dashboard.
- 🧠 Kesan daun cili dengan YOLO, termasuk bilangan daun dan anggaran daun stres.
- 💧 Pantau kelembapan tanah dan gunakan had penyiraman maksimum **2 kali sehari**.
- 🌿 Urus logik baja dengan tempoh bertenang **7 hari**.
- 📊 Paparkan status pam, diagnosis, statistik sesi dan sejarah bacaan.
- 🇲🇾 Gunakan zon waktu Malaysia (`Asia/Kuala_Lumpur`) untuk cap masa dan pengiraan harian.
- 🐳 Sediakan `Dockerfile` untuk menjalankan aplikasi dalam container.

## 🧩 Gambaran sistem

```text
ESP32-CAM ── imej + bacaan tanah ──┐
                                  ├──> Flask + model YOLO ──> Dashboard web
ESP32 sensor ── bacaan tanah ─────┘             │
ESP32 actuator <──────── status pam ────────────┘
```

## 🗂️ Struktur projek

| Fail | Kegunaan |
|---|---|
| `app.py` | Backend Flask, pemprosesan imej, logik sensor/pam dan endpoint API |
| `templates/dashboard.html` | Dashboard pemantauan web |
| `best.pt` | Model YOLO yang dimuatkan oleh backend |
| `yolov8n.pt` | Fail model tambahan; tidak dimuatkan secara langsung oleh `app.py` |
| `plant_disease_model.h5` | Fail model tambahan; tidak dimuatkan secara langsung oleh `app.py` |
| `requirements.txt` | Kebergantungan Python |
| `Dockerfile` | Konfigurasi imej Docker |
| `SETUP_GUIDE.md` | Panduan setup sistem/hardware tambahan |

## 🚀 Mula dengan pantas

### Keperluan

- Python **3.11** (sepadan dengan imej Docker projek)
- `pip`
- Fail model `best.pt` di direktori utama projek

### Jalankan secara lokal

```powershell
# Cipta dan aktifkan virtual environment (Windows)
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# Pasang dependencies
python -m pip install -r requirements.txt

# Jalankan backend dan dashboard
python app.py
```

Buka **http://localhost:5000** dalam browser. Semak backend di **http://localhost:5000/health** — respons `model_loaded: true` menunjukkan model YOLO berjaya dimuatkan.

> Pemasangan PyTorch dan Ultralytics mungkin mengambil masa dan ruang yang agak besar. Jika model gagal dimuatkan, semak log terminal serta kewujudan fail `best.pt`.

### Jalankan dengan Docker

```powershell
docker build -t chili-stress-detection .
docker run --rm -p 8080:8080 chili-stress-detection
```

Buka **http://localhost:8080**. Container menggunakan Gunicorn dan port `8080`.

## 🔌 Sambungan ESP32 dan API

Dashboard menggunakan origin yang sama dengan backend. Konfigurasikan firmware ESP32 supaya menghantar permintaan ke alamat backend yang betul.

| Kaedah | Endpoint | Tujuan |
|---|---|---|
| `GET` | `/` | Dashboard |
| `GET` | `/health` | Status backend dan ketersediaan model |
| `POST` | `/detect` | Hantar imej mentah (`application/octet-stream`) dari ESP32-CAM |
| `POST` | `/soil` | Hantar JSON bacaan sensor tanah |
| `GET` | `/pump-status` | Dapatkan status pam dan tracker |
| `GET` | `/history` | Dapatkan sehingga 20 rekod terkini |
| `GET` | `/stats` | Dapatkan statistik sesi |
| `GET` | `/latest-image-data` | Dapatkan imej terkini untuk dashboard |

Contoh payload untuk `/soil`:

```json
{
  "soil_raw": 2500,
  "soil_percent": 45.0
}
```

Endpoint `/detect` juga boleh menerima header pilihan `X-Soil-Raw` dan `X-Soil-Percent`. Untuk butiran debug model dan tracker, lihat endpoint `/debug/model-info`, `/debug/last-detections`, dan `/debug/reset-trackers`.

## ⚙️ Tetapan penting

Nilai ambang dan had operasi ditetapkan dalam `app.py`:

- Tanah dianggap kering apabila kelembapan kurang daripada **30%**.
- Pam air dihadkan kepada **2 kali sehari**.
- Baja mempunyai tempoh bertenang **7 hari**.
- Analisis penuh YOLO dijalankan setiap **3 frame**; imej terkini masih dikemas kini setiap frame.
- Model yang digunakan ialah `best.pt` (`MODEL_PATH`).

Laraskan nilai tersebut mengikut sensor, perkakasan dan model yang digunakan. Uji logik pam dengan selamat sebelum menyambungkan pam sebenar.

> **Nota operasi:** Sejarah bacaan dan tracker pam disimpan dalam memori proses, bukan pangkalan data. Data ini akan bermula semula apabila backend dimulakan semula. Endpoint reset tracker disediakan untuk ujian; lindungi backend sebelum mendedahkannya kepada rangkaian awam.

## 🛠️ Teknologi

**Python · Flask · Ultralytics YOLO · OpenCV · NumPy · ESP32-CAM · HTML/CSS/JavaScript · Docker**

---

<div align="center">

Dibangunkan sebagai projek IoT UiTM · **ITT569**

</div>
