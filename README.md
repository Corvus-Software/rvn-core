# rvn-core 🔥

> **High-performance motorcycle data logger firmware** - The beating heart of the Raven ecosystem

[![STM32](https://img.shields.io/badge/STM32-L432KC-blue.svg)](https://www.st.com/en/microcontrollers-microprocessors/stm32l432kc.html)
[![ESP32](https://img.shields.io/badge/ESP32-C3--MINI-red.svg)](https://www.espressif.com/en/products/modules/esp32-c3-mini-1)
[![GPS](https://img.shields.io/badge/GPS-10Hz-green.svg)](https://www.u-blox.com/en/product/neo-m8-series)
[![IMU](https://img.shields.io/badge/IMU-9--axis-orange.svg)](https://www.st.com/en/mems-and-sensors/lsm9ds1.html)

**rvn-core** is the embedded firmware powering the Raven motorcycle data logger. Dual-processor architecture captures GPS coordinates, 9-axis IMU data, and telemetry at 10Hz with millisecond precision.

## 🏍️ System Overview

```
┌─────────────────┐    UART     ┌─────────────────┐    BLE      ┌─────────────┐
│   STM32L432KC   │◄──────────► │  ESP32-C3-MINI  │◄──────────► │   iPhone    │
│  (Data Logger)  │             │   (Wireless)    │             │  Raven App  │
└─────────────────┘             └─────────────────┘             └─────────────┘
         │                               │
         │ SPI/UART                      │ 
         ▼                               ▼
┌─────────────────┐             ┌─────────────────┐
│   Sensors       │             │ throttle-ai     │
│ • GPS (10Hz)    │             │  (Backend)      │
│ • IMU (9-axis)  │             │                 │
│ • SD Logging    │             │                 │
└─────────────────┘             └─────────────────┘
```

## ⚡ Features

- **10Hz GPS tracking** with auto-configuration (NEO-M8N)
- **9-axis IMU data** - accelerometer, gyroscope, magnetometer (LSM9DS1)
- **Binary data logging** to SD card with session management
- **Real-time telemetry** via BLE to iPhone app
- **Dual-processor architecture** for optimal performance
- **Session-based recording** with unique ID generation
- **Hardware timer-driven sampling** for millisecond precision

## 📊 Data Captured

**Per 10Hz sample (~65 bytes):**
- Session ID & timestamps
- GPS: latitude, longitude, altitude, speed, fix quality
- Accelerometer: X, Y, Z (m/s²)
- Gyroscope: X, Y, Z (°/s)  
- Magnetometer: X, Y, Z (μT)
- Data validity flags & checksum

## 🛠️ Hardware Requirements

| Component | Part | Function |
|-----------|------|----------|
| **Main MCU** | STM32L432KC | Data processing & logging |
| **Wireless** | ESP32-C3-MINI-1-N4 | BLE communication |
| **GPS** | u-blox NEO-M8N | 10Hz positioning |
| **IMU** | LSM9DS1 | 9-axis motion sensing |
| **Storage** | MicroSD card | Data logging |
| **Power** | Li-ion + TP4057 charger | Portable operation |

## 🚀 Quick Start

### Prerequisites
- **PlatformIO** with VS Code
- **ST-Link V2/V3** programmer 
- **USB-UART adapter** for ESP32
- **Pogo pin programming setup**

### 1. Clone Repository
```bash
git clone https://github.com/yourusername/rvn-core.git
cd rvn-core
```

### 2. STM32 Setup
```bash
cd stm32
pio run --target upload
```

### 3. ESP32 Setup  
```bash
cd esp32
pio run --target upload
```

### 4. Programming Interface
**STM32 (ST-Link):**
- SWDIO, SWCLK, GND, VCC via pogo pins

**ESP32 (UART):**
- TX, RX, IO9 (boot), EN (reset), GND via pogo pins

## 📁 Project Structure

```
rvn-core/
├── stm32/                 # STM32L432KC firmware
│   ├── src/
│   │   ├── main.cpp      # Main application
│   │   ├── gps.cpp       # GPS configuration & parsing
│   │   ├── imu.cpp       # LSM9DS1 sensor interface
│   │   ├── sd_logger.cpp # Data logging system
│   │   └── uart_comm.cpp # ESP32 communication
│   ├── lib/              # Libraries
│   └── platformio.ini    # PlatformIO config
├── esp32/                # ESP32-C3 firmware  
│   ├── src/
│   │   ├── main.cpp      # BLE server & data relay
│   │   ├── ble_server.cpp# iPhone connectivity
│   │   └── stm32_comm.cpp# STM32 communication
│   ├── lib/
│   └── platformio.ini
├── docs/                 # Documentation
├── tools/                # Programming utilities
└── README.md
```

## 🔧 Configuration

### GPS Auto-Configuration
The system automatically detects GPS update rate and configures NEO-M8N to 10Hz:
```cpp
// Sends UBX commands to set 100ms update rate
const uint8_t ubx_set_10hz[] = {
    0xB5, 0x62, 0x06, 0x08, 0x06, 0x00,
    0x64, 0x00, 0x01, 0x00, 0x01, 0x00,
    0x7A, 0x12
};
```

### Session Management
- **Session ID** generated on first GPS fix
- **Binary files** stored as `/sessions/session_[ID].bin`
- **Metadata** includes duration, sample count, GPS fixes

## 📱 iPhone Integration

**BLE Protocol:**
- **Service**: Custom motorcycle data service
- **Data Transfer**: Chunked binary streaming  
- **Commands**: List sessions, get session data, system status
- **Real-time**: Battery level, GPS fix status, logging state

## 🔬 Development

### Debugging
```bash
# STM32 serial debug (115200 baud)
pio device monitor --baud 115200

# ESP32 BLE debug
pio device monitor --baud 115200 --port /dev/ttyUSB1
```

### Adding Sensors
1. Update `SampleData_t` structure in `main.cpp`
2. Add sensor initialization in `setup()`  
3. Add sensor reading in `timer_10hz_callback()`
4. Update buffer size if needed

## 🔋 Power Management

- **Operating voltage**: 3.3V regulated
- **Current draw**: ~80-100mA (GPS + WiFi + logging)
- **Battery**: Li-ion with USB-C charging
- **Sleep modes**: Not implemented (always-on logger)

## 🛡️ Data Integrity

- **Hardware timers** ensure precise 10Hz sampling
- **Double buffering** prevents data loss during SD writes
- **Checksums** on binary data packets
- **Session validation** with start/end timestamps

## 🔗 Ecosystem

Part of the **Raven** motorcycle telemetry system:

- **rvn** - iOS app for data visualization
- **rvn-web** - Web dashboard  
- **rvn-core** - This firmware (data logger)
- **throttle-ai** - Backend data processing
- **rvn-board** - Hardware design files

---

**Built with ⚡ for the open road** 🏍️💨
