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
