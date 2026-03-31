# BBQ Meat Thermometer

ESPHome-based wireless meat thermometer for BBQ smoking/grilling.

## Hardware
- **MCU:** Seeed Xiao ESP32-C6
- **Display:** SH1106 128x64 OLED (I2C)
- **Sensor:** NTC thermistor probe
- **Power:** 18650 Li-ion with voltage divider for monitoring

## Features
- Real-time meat temperature monitoring
- Battery level tracking
- WiFi signal strength
- Home Assistant integration
- Deep sleep support for battery conservation

## Pinout (Xiao ESP32-C6)
| Function | GPIO |
|----------|------|
| NTC Probe | GPIO2 (A0) |
| Battery ADC | GPIO3 (A1) |
| I2C SDA | GPIO22 (D4) |
| I2C SCL | GPIO23 (D5) |

## Calibration
Update the NTC calibration values after testing:
- `b_constant`: Usually 3950 for typical NTC probes
- `reference_resistance`: Resistance at 25°C (typically 10K)
- `reference_temperature`: 25°C

## Usage
1. Copy `secrets.yaml.example` to `secrets.yaml` and fill in your values
2. Compile and flash via ESPHome Dashboard
3. Add to Home Assistant via discovery or manual integration
