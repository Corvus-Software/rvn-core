# Pin Mapping - RVN Core Data Logger

## STM32L432KC Pin Assignments

### Power & System
| Pin | Function | Connection | Notes |
|-----|----------|------------|-------|
| VDD (1,17) | Power 3.3V | +3.3V rail | From TPS63001 regulator |
| VSS (16) | Ground | GND | System ground |
| VDDA (5) | Analog Power | +3.3V | Analog supply |
| NRST (4) | Reset | Reset circuit | External reset button |

### Programming Interface
| Pin | Function | Connection | Notes |
|-----|----------|------------|-------|
| PA13 (23) | SWDIO | Programming pad | ST-Link SWDIO |
| PA14 (24) | SWCLK | Programming pad | ST-Link SWCLK |

### GPS Module (NEO-M8N)
| STM32 Pin | Function | GPS Pin | Notes |
|-----------|----------|---------|-------|
| PA2 (8) | UART2_TX | RXD (21) | STM32 → GPS |
| PA3 (9) | UART2_RX | TXD (20) | GPS → STM32 |

### ESP32 Communication
| STM32 Pin | Function | ESP32 Pin | Notes |
|-----------|----------|-----------|-------|
| PA9 (19) | UART1_TX | RXD0 (30) | STM32 → ESP32 |
| PA10 (20) | UART1_RX | TXD0 (31) | ESP32 → STM32 |

### IMU (LSM9DS1) - SPI Interface
| STM32 Pin | Function | IMU Pin | Notes |
|-----------|----------|---------|-------|
| PB3 (26) | SPI_SCK | SCL/SPC (19) | SPI Clock |
| PB4 (27) | SPI_MISO | SDA/SDI/SDO (4) | Master In Slave Out |
| PB5 (28) | SPI_MOSI | SDA/SDI/SDO (4) | Master Out Slave In |
| PA4 (10) | SPI_CS1 | CS_A/G (7) | Accel/Gyro Chip Select |
| PA5 (11) | SPI_CS2 | CS_M (8) | Magnetometer Chip Select |
| PA6 (12) | SPI_INT | INT1_A/G (11) | Interrupt 1 |
| PA7 (13) | SPI_INT | INT2_A/G (12) | Interrupt 2 |

### SD Card - SPI Interface
| STM32 Pin | Function | SD Pin | Notes |
|-----------|----------|--------|-------|
| PB3 (26) | SPI_SCK | CLK (5) | Shared with IMU |
| PB4 (27) | SPI_MISO | DAT0 (7) | Shared with IMU |
| PB5 (28) | SPI_MOSI | CMD (3) | Shared with IMU |
| PB0 (14) | SPI_CS3 | DAT3/CD (2) | SD Card Chip Select |

### Status & Control
| STM32 Pin | Function | Connection | Notes |
|-----------|----------|------------|-------|
| PB1 (15) | LED_G | Green LED | Status indicator |
| PB6 (29) | Button | SW1 | Reset button input |
| PB7 (30) | Button | SW2 | User button input |

### Crystal Oscillator
| STM32 Pin | Function | Component | Notes |
|-----------|----------|-----------|-------|
| PC14 (2) | OSC_IN | Y1 32.768kHz | Low speed external oscillator |
| PC15 (3) | OSC_OUT | Y1 32.768kHz | LSE output |

### Battery Monitoring
| STM32 Pin | Function | Connection | Notes |
|-----------|----------|------------|-------|
| PA0 (6) | BAT_ADC | Battery voltage | ADC input for battery level |
| PA1 (7) | CHRG_STAT | TP4057 CHRG | Charging status |

## ESP32-C3-MINI Pin Assignments

### Programming Interface
| Pin | Function | Connection | Notes |
|-----|----------|------------|-------|
| RXD0 (30) | UART_RX | Programming pad | USB-UART RX |
| TXD0 (31) | UART_TX | Programming pad | USB-UART TX |
| IO9 (23) | BOOT | Programming pad | Boot mode selection |
| EN (8) | RESET | Programming pad | Reset input |
| GND (2,14,36) | Ground | GND | System ground |
| 3V3 (3) | Power | +3.3V | Power supply |

### STM32 Communication
| ESP32 Pin | Function | STM32 Pin | Notes |
|-----------|----------|-----------|-------|
| RXD0 (30) | UART_RX | PA9 (UART1_TX) | ESP32 ← STM32 |
| TXD0 (31) | UART_TX | PA10 (UART1_RX) | ESP32 → STM32 |

### GPIO Available for Future Use
| Pin | Function | Notes |
|-----|----------|-------|
| IO0 (12) | GPIO | Available |
| IO1 (13) | GPIO | Available |
| IO2 (5) | GPIO | Available |
| IO3 (6) | GPIO | Available |
| IO4 (18) | GPIO | Available |
| IO5 (19) | GPIO | Available |
| IO6 (20) | GPIO | Available |
| IO7 (21) | GPIO | Available |
| IO8 (22) | GPIO | Available |
| IO10 (16) | GPIO | Available |
| IO18 (26) | GPIO | Available |
| IO19 (27) | GPIO | Available |

## Power Management (TPS63001)

### Input/Output
| Pin | Function | Connection | Notes |
|-----|----------|------------|-------|
| VIN (5) | Input | +5V (USB/Battery) | 3.0V - 5.5V input |
| VOUT (1) | Output | +3.3V | Regulated 3.3V output |
| EN (6) | Enable | +5V | Always enabled |
| GND (9) | Ground | GND | Power ground |
| PGND (3) | Power Ground | GND | High current ground |

### Feedback & Control
| Pin | Function | Component | Notes |
|-----|----------|-----------|-------|
| FB (10) | Feedback | Voltage divider | Output voltage sensing |
| L1 (4) | Inductor | L1 1.5uH | Energy storage |
| L2 (2) | Inductor | L1 1.5uH | Shared inductor |

## Battery Charger (TP4057)

| Pin | Function | Connection | Notes |
|-----|----------|------------|-------|
| BAT (3) | Battery | Li-ion battery | 4.2V max |
| VCC (4) | Input | +5V (USB) | Charging input |
| CHRG (1) | Status | LED + STM32 | Charging indicator |
| STDBY (5) | Status | LED | Standby indicator |
| GND (2) | Ground | GND | System ground |
| PROG (6) | Current | Resistor | Charge current setting |

## Crystal & Timing

### Main Crystal (Y1)
| Component | Value | Pins | Notes |
|-----------|-------|------|-------|
| Y1 | 32.768kHz | PC14, PC15 | Low speed external |
| C1, C2 | 6pF | Load capacitors | Crystal loading |

## Status LEDs

| LED | Color | Pin | Function |
|-----|-------|-----|----------|
| D1 | Red | TP4057 CHRG | Charging status |
| D2 | Green | +5V via R6 | Power indicator |
| D3 | Green | PB1 via R7 | GPS/System status |

## External Connectors

### USB-C (J2)
| Pin | Function | Notes |
|-----|----------|-------|
| VBUS | +5V | Power input |
| D+, D- | USB Data | Charging only |
| CC1, CC2 | Configuration | USB-C detection |
| GND | Ground | System ground |

### Battery Connector (J3)
| Pin | Function | Notes |
|-----|----------|-------|
| 1 | Battery + | Li-ion positive |
| 2 | Battery - | Ground |

### GPS Antenna (J6)
| Pin | Function | Notes |
|-----|----------|-------|
| Center | RF Signal | GPS antenna input |
| Shield | Ground | RF ground |

### Test Points
| Designator | Function | Notes |
|------------|----------|-------|
| J4, J5, J7-J14 | Test points | Various signals |
