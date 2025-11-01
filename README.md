# ESP32 Leaf Camera - Plant Disease Detection System

A distributed IoT system for real-time plant disease detection using ESP32 microcontrollers, camera module, and AI-powered analysis on Raspberry Pi 5.

# Project Links

- Project Demonstrations (YouTube): https://youtu.be/vgtTrY1KByk
- Live Website: https://plant-sense-now.web.app
- REST API (Vercel): https://plant-disease-detection-server-one.vercel.app/diseases
- Frontend (Client): https://github.com/mahfuzhasanreza/plant-sense-client
- Backend (Server): https://github.com/mahfuzhasanreza/plant-sense-server
- Model (ResNet50): https://github.com/mahfuzhasanreza/plant-sense-resnet50
- ESP32 Modules: https://github.com/mahfuzhasanreza/plant-sense-esp32-modules

## 🌿 Overview

This project implements an automated plant disease detection system using:
- **ESP32-CAM**: Captures leaf images with flash support
- **ESP32 Hub**: Receives images, uploads to server, displays results on OLED
- **Raspberry Pi 5 Server**: Runs TensorFlow Lite model for disease classification
- **Cloud Integration**: Posts results to remote monitoring dashboard

The system identifies 38 different plant diseases across multiple species including tomato, apple, corn, grape, potato, and more, providing instant diagnosis and treatment recommendations.

## 🏗️ System Architecture

```
┌─────────────────┐
│   ESP32-CAM     │ ──UART──┐
│  (Image Capture)│         │
└─────────────────┘         │
                            ▼
                    ┌───────────────┐
                    │   ESP32 Hub   │ ──WiFi──┐
                    │  (WiFi Bridge)│         │
                    │  + OLED Display│         │
                    │  + LEDs/Buzzer│         │
                    └───────────────┘         │
                                              ▼
                                      ┌──────────────┐
                                      │ Raspberry Pi 5│
                                      │   (FastAPI)   │
                                      │  TFLite Model │
                                      └──────────────┘
                                              │
                                              ▼
                                      ┌──────────────┐
                                      │ Cloud Server  │
                                      │   (Vercel)    │
                                      └──────────────┘
```

## ✨ Features

### Hardware
- 📸 **ESP32-CAM Image Capture**: High-quality JPEG images with LED flash
- 🖥️ **OLED Display**: Real-time status and results (SH1106/SSD1306)
- 💡 **Visual Indicators**: RGB LEDs for status feedback
- 🔊 **Audio Alerts**: Buzzer for operation confirmation
- ⚡ **UART Communication**: Fast 921.6Kbps transfer between ESP32s

### Software
- 🤖 **AI-Powered Analysis**: ResNet50-based TFLite model (38 plant classes)
- 🔄 **Multi-mode Detection**: Model inference + heuristic fallback
- 📊 **Detailed Metrics**: Confidence scores, color analysis, chlorophyll estimation
- 🌐 **RESTful API**: FastAPI server with upload and result endpoints
- ☁️ **Cloud Sync**: Automatic result posting to remote dashboard
- 🎯 **Treatment Recommendations**: Disease-specific solutions database

## 🔧 Hardware Requirements

### ESP32-CAM Module
- ESP32-CAM (AI-Thinker) board
- OV2640 camera module
- AMS1117 5V to 3.3V regulator (onboard)
- LED flash (GPIO 4)

### ESP32 Hub Module
- ESP32-WROOM-32 development board
- SH1106 or SSD1306 OLED display (128x64, I2C)
- Green LED (GPIO 27) + 220Ω resistor
- Red LED (GPIO 26) + 220Ω resistor
- Buzzer (GPIO 25)
- Breadboard and jumper wires

### Server
- Raspberry Pi 5 (or any Linux machine)
- Python 3.8+
- TensorFlow Lite Runtime

## 📦 Software Requirements

### PlatformIO Dependencies
```ini
# ESP32-CAM
- espressif32 platform
- Arduino framework
- ArduinoJson ^7.4.2

# ESP32 Hub
- espressif32 platform
- Arduino framework
- Adafruit SSD1306 ^2.5.10
- Adafruit GFX Library ^1.11.11
- Adafruit SH110X ^2.1.11
- ArduinoJson ^7.4.2
```

### Raspberry Pi Python Dependencies
```bash
pip install fastapi uvicorn pillow numpy requests tflite-runtime
```

## 🚀 Getting Started

### 1. Hardware Setup

#### ESP32-CAM Connections
```
ESP32-CAM    →    ESP32 Hub
TX (GPIO1)   →    RX2 (GPIO16)
RX (GPIO3)   →    TX2 (GPIO17)
GND          →    GND
```

#### ESP32 Hub Connections
```
GPIO 21 (SDA)  →  OLED SDA
GPIO 22 (SCL)  →  OLED SCL
GPIO 27        →  Green LED (+ 220Ω → GND)
GPIO 26        →  Red LED (+ 220Ω → GND)
GPIO 25        →  Buzzer (+ GND)
3.3V           →  OLED VCC
GND            →  OLED GND
```

### 2. Configure WiFi and Server

Edit `src/hub/main.cpp`:
```cpp
#define PI5_UPLOAD_URL "http://YOUR_PI_IP:8000/upload"
#define PI5_RESULT_URL "http://YOUR_PI_IP:8000/result"

const char* ssid = "YOUR_SSID";
const char* password = "YOUR_PASSWORD";
```

### 3. Flash ESP32 Modules

#### Upload to ESP32-CAM
```bash
# Put ESP32-CAM in download mode (GPIO0 to GND while pressing RST)
pio run -e esp32cam -t upload
```

#### Upload to ESP32 Hub
```bash
pio run -e esp32hub -t upload
```

### 4. Setup Raspberry Pi Server

```bash
cd /path/to/project
python3 pi5_server.py
# or use the provided script
./run_pi5_server.sh
```

Server will start on `http://0.0.0.0:8000`

## 📝 Usage

1. **Power On**: Boot both ESP32 modules
2. **Wait for WiFi**: Hub connects to network (OLED shows status)
3. **Capture**: Place leaf under camera, system automatically captures
4. **Transfer**: Image sent via UART to Hub (red LED blinks)
5. **Upload**: Hub uploads to Pi5 over WiFi
6. **Analysis**: Pi5 runs AI model and returns results
7. **Display**: Results shown on OLED with disease name and solution
8. **Feedback**: Green LED pulses, buzzer beeps on success

## 🎯 Supported Plant Diseases

The system detects **38 classes** including:

### Fruits
- **Apple**: Scab, Black Rot, Cedar Rust, Healthy
- **Cherry**: Powdery Mildew, Healthy
- **Peach**: Bacterial Spot, Healthy
- **Orange**: Huanglongbing (Citrus Greening)
- **Grape**: Black Rot, Esca, Leaf Blight, Healthy
- **Strawberry**: Leaf Scorch, Healthy
- **Blueberry**: Healthy
- **Raspberry**: Healthy

### Vegetables
- **Tomato**: 8 diseases including Early/Late Blight, Leaf Mold, Mosaic Virus
- **Potato**: Early Blight, Late Blight, Healthy
- **Pepper**: Bacterial Spot, Healthy
- **Corn**: Gray Leaf Spot, Common Rust, Northern Leaf Blight, Healthy
- **Soybean**: Healthy
- **Squash**: Powdery Mildew

## 📊 API Endpoints

### POST /upload
Upload leaf image for analysis
```bash
curl -X POST http://PI_IP:8000/upload \
  --data-binary "@leaf_image.jpg" \
  -H "Content-Type: image/jpeg"
```

**Response:**
```json
{
  "status": "success",
  "timestamp": "20250101_123456",
  "filename": "image_20250101_123456.jpg",
  "leaf_name": "Tomato Early Blight",
  "disease": "Tomato Early Blight",
  "solution": "Mulch to reduce soil splash and use a chlorothalonil fungicide weekly.",
  "metrics": {
    "model_confidence": 0.9234,
    "analysis_source": "tflite_model"
  }
}
```

### GET /result
Retrieve latest analysis result
```bash
curl http://PI_IP:8000/result
```

## 🔍 Model Information

- **Architecture**: ResNet50
- **Format**: TensorFlow Lite (Float32)
- **Input**: 224×224×3 RGB images
- **Output**: 38 class probabilities
- **Dataset**: PlantVillage
- **Fallback**: Heuristic color-based analysis when model unavailable

## 📁 Project Structure

```
esp32-leaf-cam/
├── src/
│   ├── cam/
│   │   └── main.cpp          # ESP32-CAM firmware
│   └── hub/
│       └── main.cpp          # ESP32 Hub firmware
├── platformio.ini            # PlatformIO configuration
├── pi5_server.py             # FastAPI server + inference
├── leaf_resnet50_float.tflite # TFLite model
├── leaf_labels.txt           # Class labels
├── leaf_labels.json          # Metadata (optional)
├── kaggle.json               # Kaggle credentials (optional)
├── run_pi5_server.sh         # Server startup script
├── upload/                   # Saved images directory
└── README.md                 # This file
```

## 🐛 Troubleshooting

### ESP32-CAM Won't Flash
- Connect GPIO0 to GND before powering on
- Press RST button while GPIO0 is grounded
- Check baud rate (115200 for upload)
- Verify USB-to-Serial adapter voltage (3.3V logic)

### UART Transfer Fails
- Check TX/RX cross-connection
- Verify common ground between modules
- Reduce baud rate if wires are long
- Check Serial2 pins (16, 17) on Hub

### WiFi Connection Issues
- Verify SSID and password in code
- Check 2.4GHz WiFi (ESP32 doesn't support 5GHz)
- Move closer to router
- Check OLED for connection status

### Model Not Loading
- Ensure `leaf_resnet50_float.tflite` exists
- Install TFLite runtime: `pip install tflite-runtime`
- Check file permissions
- Fallback heuristic analysis will run automatically

### OLED Display Blank
- Verify I2C address (0x3C or 0x3D)
- Check SDA/SCL connections
- Adjust `OLED_ADDR` in `platformio.ini`
- Test with I2C scanner code

## 🎛️ Configuration Options

### OLED Display Type
In `platformio.ini` under `[env:esp32hub]`:
```ini
# For SH1106 display
-D USE_SH1106=1
-D OLED_ADDR=0x3C

# For SSD1306 display
-D USE_SH1106=0
-D OLED_ADDR=0x3C
```

### UART Baud Rate
Adjust in both `src/cam/main.cpp` and `src/hub/main.cpp`:
```cpp
#define CAM_UART_BAUD 921600  // or 115200 for longer wires
```

### Cloud URL
In `pi5_server.py`:
```python
_CLOUD_POST_URL = "https://your-server.com/diseases"
```

## 📜 License

This project is open-source. Feel free to modify and distribute as needed for educational and commercial purposes.

## 👥 Author

- Mahfuz Hasan Reza (@mahfuzhasanreza)

## 🙏 Acknowledgments

- PlantVillage Dataset for training data
- TensorFlow Lite for model optimization
- Adafruit for display libraries
- PlatformIO for build system

## 📧 Contact

For questions, issues, or contributions, please open an issue on the GitHub repository or contact the maintainer.
