# Hardware Troubleshooting Guide - RVN Core

## Power Issues

### Board Not Powering On
**Symptoms:** No LEDs, no USB detection, completely dead

**Check List:**
1. **USB-C Connection**
   - Verify cable is data-capable (not charge-only)
   - Try different USB ports/chargers
   - Check for 5V on VBUS pin

2. **Battery Connection**
   - Verify battery voltage (3.0V - 4.2V)
   - Check battery connector polarity
   - Test battery under load

3. **Power Regulation (TPS63001)**
   - Measure input voltage at VIN pin (5V)
   - Check output voltage at VOUT pin (3.3V)
   - Verify enable pin is HIGH
   - Check inductor L1 for continuity

**Solutions:**
- Replace USB cable with known-good data cable
- Charge battery externally if voltage < 3.0V
- Verify TPS63001 feedback network (FB pin voltage should be ~0.6V)

### Voltage Regulation Issues
**Symptoms:** Low 3.3V output, processor resets, unstable operation

**Measurements:**
| Test Point | Expected | Tolerance |
|------------|----------|-----------|
| USB VBUS | 5.0V | ±0.25V |
| TPS63001 VIN | 5.0V | ±0.25V |
| TPS63001 VOUT | 3.3V | ±0.1V |
| STM32 VDD | 3.3V | ±0.1V |
| ESP32 3V3 | 3.3V | ±0.1V |

**Common Causes:**
- Damaged TPS63001 regulator
- Shorted output capacitors
- Overload condition (check current draw)
- Poor solder joints on power components

## Programming Issues

### STM32 Programming Failures

**"Target Not Found" Error:**
1. **Connection Issues:**
   - Verify SWDIO/SWCLK pad connections
   - Check for cold solder joints
   - Ensure stable mechanical contact

2. **Power Issues:**
   - Confirm 3.3V on STM32 VDD pins
   - Check that NRST is not held low
   - Verify crystal oscillator (if used)

3. **Software Configuration:**
   - Try lower SWD frequency (1MHz or 480kHz)
   - Use "Connect Under Reset" option
   - Try different ST-Link programmer

**Programming Verification Errors:**
- Flash memory corruption → Try mass erase
- Voltage instability → Add external power supply
- Timing issues → Reduce programming speed

### ESP32 Programming Failures

**Boot Mode Issues:**
1. **Cannot Enter Programming Mode:**
   - Verify IO9 is pulled LOW during reset
   - Check EN pin reset functionality
   - Ensure stable power during boot sequence

2. **UART Communication Errors:**
   - Verify TX/RX pin connections (crossed correctly)
   - Try different baud rates (115200, 57600, 9600)
   - Check for correct voltage levels (3.3V)

**Upload Timeouts:**
- Increase timeout values in Arduino IDE
- Try manual reset timing
- Check for electromagnetic interference

## Sensor Issues

### GPS Module (NEO-M8N) Problems

**No GPS Data Received:**
1. **UART Communication:**
   - Verify TX/RX connections to STM32
   - Check baud rate (9600 default)
   - Monitor UART traffic with oscilloscope/logic analyzer

2. **Power Issues:**
   - Confirm 3.3V supply to GPS module
   - Check current draw (~25mA)
   - Verify backup battery (if present)

3. **Antenna Issues:**
   - Check antenna connection (J6)
   - Verify antenna cable continuity
   - Test with different/external antenna

**GPS Takes Long Time to Fix:**
- Cold start can take 30+ seconds
- Check satellite reception (outdoor test)
- Verify antenna gain and placement
- Consider assisted GPS configuration

**10Hz Configuration Fails:**
- Check UBX command transmission
- Verify GPS firmware version
- Try step-by-step rate increase (1Hz → 5Hz → 10Hz)

### IMU (LSM9DS1) Problems

**No IMU Data:**
1. **SPI Communication:**
   - Verify SPI bus connections (MOSI, MISO, SCK)
   - Check chip select signals (CS_AG, CS_M)
   - Monitor SPI transactions with logic analyzer
