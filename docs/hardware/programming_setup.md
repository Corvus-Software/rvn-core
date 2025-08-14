# Programming Setup Guide - RVN Core

## Overview
The RVN Core uses dual processors requiring separate programming interfaces:
- **STM32L432KC**: ST-Link via SWD (4-pin)
- **ESP32-C3-MINI**: UART bootloader (5-pin)

## Required Hardware

### Programming Tools
| Tool | Purpose | Cost | Notes |
|------|---------|------|-------|
| ST-Link V2/V3 | STM32 programming | $15-25 | Official or clone |
| USB-UART Adapter | ESP32 programming | $8-12 | CP2102 or FT232 |
| 5-pin Pogo Connectors | Board connection | $15-20 | Spring-loaded pins |
| Test Hook Clips | Alternative method | $8-15 | Micro grabber clips |

### Software Requirements
- **STM32CubeIDE** or **PlatformIO** with VS Code
- **Arduino IDE** with ESP32 board package
- **STM32CubeProgrammer** (optional, for standalone programming)

## Programming Pad Locations

### STM32 Programming Pads
Located near the STM32L432KC chip:
- **SWDIO**: PA13 (pin 23) - Data line
- **SWCLK**: PA14 (pin 24) - Clock line  
- **GND**: System ground
- **VCC**: 3.3V power (can be supplied by ST-Link)

### ESP32 Programming Pads
Located around the ESP32-C3-MINI module:
- **TX**: TXD0 (pin 31) - ESP32 transmit
- **RX**: RXD0 (pin 30) - ESP32 receive
- **IO9**: GPIO9 (pin 23) - Boot mode pin
- **EN**: Reset (pin 8) - Reset input
- **GND**: System ground

## Connection Methods

### Method 1: Pogo Pin Programming Jig (Recommended)

**Advantages:**
- Reliable, repeatable connections
- No risk of pad damage
- Fast programming cycles
- Professional appearance

**STM32 Jig Wiring:**
ST-Link Connector    Pogo Pin Jig
Pin 1 (VCC)    →     Pogo Pin 1 (VCC pad)
Pin 2 (SWCLK)  →     Pogo Pin 2 (SWCLK pad)
Pin 3 (GND)    →     Pogo Pin 3 (GND pad)
Pin 4 (SWDIO)  →     Pogo Pin 4 (SWDIO pad)

**ESP32 Jig Wiring:**
USB-UART Adapter     Pogo Pin Jig
VCC (3.3V)     →     Pogo Pin 1 (VCC)
GND            →     Pogo Pin 2 (GND)
TX             →     Pogo Pin 3 (RX pad)
RX             →     Pogo Pin 4 (TX pad)
DTR/GPIO       →     Pogo Pin 5 (IO9 pad)
RTS/GPIO       →     Pogo Pin 6 (EN pad)

### Method 2: Test Hook Clips (Budget Option)

**Advantages:**
- Low cost (~$10)
- Works immediately
- No custom jig required
- Universal compatibility

**STM32 Connection:**
1. Connect ST-Link to test hook clips
2. Attach clips to programming pads:
   - Red clip → SWDIO pad
   - Black clip → SWCLK pad
   - Blue clip → GND pad
   - Yellow clip → VCC pad (optional)

**ESP32 Connection:**
1. Connect USB-UART adapter to test hook clips
2. Attach clips to programming pads
3. Use manual boot sequence (see below)

## Programming Procedures

### STM32 Programming (ST-Link Method)

**Hardware Setup:**
1. Connect ST-Link to STM32 programming pads
2. Ensure stable connection (clips or pogo pins)
3. Power the board (USB-C or external supply)

**Software Steps:**
1. Open STM32CubeIDE or PlatformIO
2. Build the STM32 project
3. Select ST-Link as programmer
4. Click "Upload" or press F5
5. Verify programming success in output log

**Troubleshooting:**
- **Connection failed**: Check pad alignment and pressure
- **Target not found**: Verify power and reset state
- **Programming error**: Try lower SWD frequency

### ESP32 Programming (UART Method)

**Hardware Setup:**
1. Connect USB-UART adapter to ESP32 programming pads
2. Ensure stable connections to all 5 pins
3. Power the board

**Manual Boot Sequence:**
1. Hold IO9 pin LOW (connect to GND)
2. Pulse EN pin LOW (reset ESP32)
3. Release EN pin (HIGH)
4. Release IO9 pin
5. ESP32 now in programming mode

**Software Steps:**
1. Open Arduino IDE with ESP32 board selected
2. Select correct COM port
3. Build the ESP32 project
4. Enter boot mode (steps above)
5. Click "Upload"
6. Reset ESP32 after programming

**Automatic Boot Circuit (Optional):**
For automatic programming, add DTR/RTS control:
DTR → 100nF capacitor → EN pin
RTS → IO9 pin (direct connection)

## Programming Scripts

### Automated STM32 Programming
```bash
#!/bin/bash
# flash_stm32.sh

echo "Programming STM32L432KC..."
cd stm32
pio run --target upload --upload-port /dev/stlink

if [ $? -eq 0 ]; then
    echo "STM32 programming successful!"
else
    echo "STM32 programming failed!"
    exit 1
fi
```
### Combined Programming Script
```bash
#!/bin/bash
# program_both.sh

echo "Programming both processors..."

# Program STM32 first
./flash_stm32.sh
if [ $? -ne 0 ]; then
    echo "Aborting due to STM32 programming failure"
    exit 1
fi

# Program ESP32 second
./flash_esp32.sh
if [ $? -ne 0 ]; then
    echo "ESP32 programming failed, but STM32 is programmed"
    exit 1
fi

echo "Both processors programmed successfully!"
```
