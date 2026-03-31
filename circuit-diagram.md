# BBQ Thermometer Circuit Diagram

## Component List

| Component | Qty | Notes |
|-----------|-----|-------|
| Seeed Xiao ESP32-C6 | 1 | MCU |
| SH1106 128x64 OLED | 1 | I2C display |
| NTC Thermistor | 1 | 10K @ 25°C, B=3950 |
| 10K Resistor | 3 | 1/4W, 5% |
| 18650 Li-ion Battery | 1 | 3.7V nominal |
| Battery Holder | 1 | For 18650 |
| JST Connector | 1 | For probe |

---

## Schematic

```
                              +-----------------------+
                              |    18650 BATTERY      |
                              |      (3.7V)           |
                              +-----------+-----------+
                                          |
                          +---------------+---------------+
                          |                               |
                          |              +----------------+----------------+
                          |              |      XIAO ESP32-C6              |
                          |              |                                 |
                          |   +----------| 3V3          GPIO22 (D4) I2C_SDA|----+
                          |   |          |                                 |    |
                          |   |   +------| GND          GPIO23 (D5) I2C_SCL|----|-+
                          |   |   |      |                                 |    | |
                          |   |   |      | GPIO2 (A0)                      |    | |
                          |   |   |      | GPIO3 (A1)                      |    | |
                          |   |   |      |                                 |    | |
                          |   |   |      +---------------------------------+    | |
                          |   |   |                                             | |
                          |   |   |                                             | |
                          |   |   |    +----------------------------------------+ |
                          |   |   |    |          SH1106 OLED (I2C)              | |
                          |   |   |    |                                         | |
                          |   |   +----| GND                                     | |
                          |   +--------| VCC                                     | |
                          |            | SDA ------------------------------------+ |
                          |            | SCL --------------------------------------+
                          |            +-------------------------------------------+
                          |
                          |        BATTERY VOLTAGE DIVIDER
                          |        (Measure at GPIO3/A1)
                          |
                          +--------[ R1: 10K ]--------+--------[ R2: 10K ]--------+
                                   (to VCC)           |                          |
                                                      |                          |
                                                   GPIO3 (A1)               GND
                                                   (ADC reading)



NTC TEMPERATURE PROBE CIRCUIT
(Measure at GPIO2/A0)

    +--------[ VCC (3.3V) ]
    |
    +--------[ NTC Probe ]--------+--------[ R_fixed: 10K ]--------+
    |        (10K @ 25°C)         |                                |
    |                             |                                |
    |                          GPIO2 (A0)                        GND
    |                          (ADC reading)
    |
    +--------[ Probe GND ]


```

---

## Pin Connections

### Xiao ESP32-C6 to OLED Display (SH1106)

| ESP32-C6 Pin | GPIO | OLED Pin | Notes |
|--------------|------|----------|-------|
| D4 | GPIO22 | SDA | I2C Data |
| D5 | GPIO23 | SCL | I2C Clock |
| 3V3 | - | VCC | Power |
| GND | - | GND | Ground |

### NTC Temperature Probe

| ESP32-C6 Pin | GPIO | Connection | Notes |
|--------------|------|------------|-------|
| A0 | GPIO2 | NTC junction | NTC to VCC, 10K to GND |

### Battery Voltage Monitor

| ESP32-C6 Pin | GPIO | Connection | Notes |
|--------------|------|------------|-------|
| A1 | GPIO3 | Divider midpoint | 2x 10K resistors |

---

## Power Supply

```
    18650 Battery (3.7V nominal, 4.2V max)
           |
           +-----> To Xiao ESP32-C6 VIN/5V pin
           |
           +-----> Voltage Divider (for monitoring)
                   |
                   +-----> GPIO3 (ADC)

    Battery range: 3.0V (empty) to 4.2V (full)
    ADC reads 0-3.3V, so divider cuts voltage in half
    Actual voltage = ADC reading × 2
```

---

## NTC Thermistor Circuit Details

```
    VCC (3.3V)
      |
      +------[ NTC Thermistor ]------+------[ 10K Resistor ]------+
                                   |                              |
                              GPIO2 (ADC)                       GND


    Temperature calculation:
    - NTC resistance decreases as temperature increases
    - ADC reading increases as temperature increases
    - Use Steinhart-Hart equation or B-parameter formula
    - B = 3950 (typical for food probe thermistors)
```

---

## Assembly Notes

1. **OLED Display**: Solder directly to Xiao pads or use a small PCB
2. **NTC Probe**: Use a JST connector for easy probe swapping
3. **Battery**: 18650 holder with JST connector to Xiao
4. **Voltage Divider**: Solder resistors directly to GPIO3 pad or on a small perfboard
5. **Enclosure**: 3D printed case (see `/home/user/Documents/3D Files/BBQ_case*.stl`)

---

## I2C Address

- SH1106 OLED default: `0x3C`

---

## Power Consumption (Estimated)

| State | Current | Notes |
|-------|---------|-------|
| Active (display on) | ~80-120mA | WiFi + OLED + MCU |
| Deep sleep | ~10-50µA | Display off, WiFi off |
| Display only | ~20-30mA | WiFi disabled |

With a typical 18650 (2500mAh), expect:
- Continuous operation: ~20-30 hours
- Deep sleep (5 min intervals): ~1-2 weeks
