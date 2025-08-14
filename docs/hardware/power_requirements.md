# Power Requirements - RVN Core

## Power Supply Overview

### Input Power Sources
- **USB-C**: 5V @ 500mA (charging + operation)
- **Li-ion Battery**: 3.0V - 4.2V @ 1000mAh (typical)
- **Regulation**: TPS63001 buck-boost converter (3.3V output)

## Current Consumption Analysis

### STM32L432KC
| Mode | Current | Notes |
|------|---------|-------|
| Active (80MHz) | ~25mA | Full processing |
| Sleep | ~2mA | Peripherals active |
| Standby | ~2µA | Ultra-low power |

### ESP32-C3-MINI
| Mode | Current | Notes |
|------|---------|-------|
| Active WiFi | ~100mA | Peak during transmission |
| Active BLE | ~20mA | BLE advertising/connected |
| Light Sleep | ~1mA | CPU stopped, peripherals on |
| Deep Sleep | ~10µA | Minimal wake sources |

### Sensors & Peripherals
| Component | Current | Notes |
|-----------|---------|-------|
| NEO-M8N GPS | ~25mA | Continuous operation |
| LSM9DS1 IMU | ~6mA | All sensors active |
| SD Card | ~100mA | Peak during write |
| Status LEDs | ~10mA | When illuminated |

## Power Budget

### Normal Operation (10Hz Logging)
| Component | Current | Duty Cycle | Average |
|-----------|---------|------------|---------|
| STM32L432KC | 25mA | 100% | 25mA |
| ESP32-C3 (BLE) | 20mA | 100% | 20mA |
| NEO-M8N GPS | 25mA | 100% | 25mA |
| LSM9DS1 IMU | 6mA | 100% | 6mA |
| SD Card | 100mA | 1% | 1mA |
| LEDs | 10mA | 50% | 5mA |
| **Total** | | | **~82mA** |

### Peak Current (SD Write + BLE Transfer)
| Component | Current | Notes |
|-----------|---------|-------|
| STM32L432KC | 25mA | Processing |
| ESP32-C3 (WiFi) | 100mA | Data transfer |
| NEO-M8N GPS | 25mA | Continuous |
| LSM9DS1 IMU | 6mA | Active |
| SD Card | 100mA | Write operation |
| LEDs | 10mA | Status indication |
| **Total** | **~266mA** | Short duration peaks |

## Battery Life Calculations

### 1000mAh Li-ion Battery
| Scenario | Current | Runtime |
|----------|---------|---------|
| Normal logging | 82mA | ~12 hours |
| Conservative estimate | 100mA | ~10 hours |
| With periodic transfers | 120mA | ~8 hours |

### Optimization Strategies
1. **GPS Power Management**: Reduce update rate when stationary
2. **ESP32 Sleep**: Deep sleep between data transfers
3. **SD Card Batching**: Buffer writes to reduce active time
4. **Dynamic LED Control**: Reduce brightness or duty cycle

## Charging Specifications

### TP4057 Li-ion Charger
| Parameter | Value | Notes |
|-----------|-------|-------|
| Input Voltage | 4.5V - 5.5V | USB-C compatible |
| Charge Current | 1A (max) | Set by PROG resistor |
| Charge Voltage | 4.2V | Li-ion standard |
| Charge Time | ~1.5 hours | For 1000mAh battery |

### Charge Status Indicators
| LED | Color | State | Meaning |
|-----|-------|-------|---------|
| D1 | Red | ON | Charging active |
| D1 | Red | OFF | Charge complete |
| D2 | Green | ON | System powered |

## Thermal Considerations

### Operating Temperature Range
- **STM32L432KC**: -40°C to +85°C
- **ESP32-C3**: -40°C to +85°C  
- **Li-ion Battery**: 0°C to +45°C (charging)
- **System Rating**: 0°C to +60°C (motorcycle use)

### Heat Dissipation
| Component | Max Power | Heat Generation |
|-----------|-----------|-----------------|
| TPS63001 | ~300mW | Minimal with 3.3V output |
| ESP32-C3 | ~500mW | During WiFi transmission |
| TP4057 | ~1W | During fast charging |

### Thermal Management
- **PCB Copper**: Ground planes for heat spreading
- **Component Placement**: Hot components separated
- **Enclosure**: Adequate ventilation for motorcycle use

## Power Supply Decoupling

### Capacitor Placement
| Component | Value | Location | Purpose |
|-----------|-------|----------|---------|
| C9 | 100nF | STM32 VDD | High frequency decoupling |
| C12 | 100nF | ESP32 3V3 | Supply filtering |
| C14 | 33µF | Power input | Bulk storage |
| C15 | 10µF | TP4057 output | Charge smoothing |
| C17 | 10µF | GPS supply | GPS power filtering |

### Power Integrity
- **Voltage Ripple**: <50mV peak-to-peak
- **Supply Regulation**: ±3% over load range
- **Startup Time**: <100ms from power-on
- **Brown-out Protection**: STM32 internal POR

## Design Margins

### Safety Factors
- **Current Budget**: 25% margin above calculated
- **Voltage Levels**: 10% margin on all supplies
- **Thermal**: 20°C margin below maximum ratings
- **Component Ratings**: 50% derating for reliability
