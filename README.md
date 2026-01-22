# ADS1256

An Arduino-compatible library for the ADS1256 24-bit analog-to-digital converter.

## Features

- 24-bit resolution with up to 30,000 SPS sampling rate
- 8 single-ended or 4 differential input channels
- Programmable gain amplifier (PGA): 1, 2, 4, 8, 16, 32, 64
- Single conversion and continuous conversion modes
- Automatic channel cycling for multi-channel acquisition
- GPIO control (4 pins)
- Self-calibration support
- Flexible SPI bus configuration

## Supported Platforms

| Platform | Status |
|----------|--------|
| Arduino AVR (Uno, Nano, Mega) | ✅ |
| Arduino Renesas | ✅ |
| ESP32 (VSPI/HSPI) | ✅ |
| STM32 (STM32duino) | ✅ |
| RP2040 (Pico, Pico W) | ✅ |
| Teensy 4.x | ✅ |

## Installation

### Arduino IDE

1. Download the library as ZIP
2. Go to **Sketch → Include Library → Add .ZIP Library**
3. Select the downloaded ZIP file

### PlatformIO

Add to your `platformio.ini`:

```ini
lib_deps =
    https://github.com/CuriousScientist0/ADS1256
```

### Manual Installation

Copy the library folder to your Arduino libraries directory:
- Windows: `Documents\Arduino\libraries\`
- macOS: `~/Documents/Arduino/libraries/`
- Linux: `~/Arduino/libraries/`

## Wiring

| ADS1256 Pin | MCU Pin | Description |
|-------------|---------|-------------|
| SCLK | SPI SCK | SPI Clock |
| DIN | SPI MOSI | SPI Data In |
| DOUT | SPI MISO | SPI Data Out |
| CS | Any GPIO | Chip Select (active low) |
| DRDY | Any GPIO | Data Ready (active low) |
| RESET | Any GPIO (optional) | Hardware Reset |
| SYNC/PDWN | Any GPIO (optional) | Sync/Power Down |
| VREF | 2.5V | Reference voltage |

## Quick Start

```cpp
#include <ADS1256.h>

// Constructor: DRDY, RESET, SYNC, CS, VREF, SPI bus
ADS1256 adc(2, ADS1256::PIN_UNUSED, 8, 10, 2.500, &SPI);

void setup() {
    Serial.begin(115200);
    adc.InitializeADC();
    
    // Configure ADC
    adc.setPGA(PGA_1);           // ±5V input range
    adc.setDRATE(DRATE_100SPS);  // 100 samples per second
    adc.setMUX(SING_0);          // Single-ended, channel 0
}

void loop() {
    long rawValue = adc.readSingle();
    float voltage = adc.convertToVoltage(rawValue);
    
    Serial.print("Voltage: ");
    Serial.println(voltage, 6);
    
    delay(100);
}
```

## API Reference

### Constructor

```cpp
ADS1256(DRDY_pin, RESET_pin, SYNC_pin, CS_pin, VREF, &SPI);
```

Use `ADS1256::PIN_UNUSED` for optional pins (RESET, SYNC).

### Initialization

| Method | Description |
|--------|-------------|
| `InitializeADC()` | Initialize the ADC with default settings |

### Configuration

| Method | Description |
|--------|-------------|
| `setMUX(mux)` | Set input multiplexer (channel selection) |
| `setPGA(pga)` | Set programmable gain amplifier |
| `setDRATE(drate)` | Set data rate (sampling frequency) |
| `setBuffer(bufen)` | Enable/disable input buffer |
| `setAutoCal(acal)` | Enable/disable auto-calibration |
| `setByteOrder(order)` | Set MSB/LSB byte order |

### Reading Data

| Method | Description |
|--------|-------------|
| `readSingle()` | Read single conversion (blocking) |
| `readSingleContinuous()` | Continuous read mode |
| `cycleSingle()` | Cycle through 8 single-ended channels |
| `cycleDifferential()` | Cycle through 4 differential channels |
| `convertToVoltage(raw)` | Convert raw value to voltage |
| `stopConversion()` | Stop continuous conversion |

### Register Access

| Method | Description |
|--------|-------------|
| `readRegister(addr)` | Read register value |
| `writeRegister(addr, value)` | Write register value |
| `sendDirectCommand(cmd)` | Send direct command to ADC |

### GPIO Control

| Method | Description |
|--------|-------------|
| `setGPIO(d0, d1, d2, d3)` | Set GPIO pin directions |
| `writeGPIO(d0, d1, d2, d3)` | Write GPIO output values |
| `readGPIO(pin)` | Read GPIO pin state |

## Input Channel Constants

### Single-Ended Inputs

| Constant | Channel |
|----------|---------|
| `SING_0` | AIN0 vs AINCOM |
| `SING_1` | AIN1 vs AINCOM |
| `SING_2` | AIN2 vs AINCOM |
| `SING_3` | AIN3 vs AINCOM |
| `SING_4` | AIN4 vs AINCOM |
| `SING_5` | AIN5 vs AINCOM |
| `SING_6` | AIN6 vs AINCOM |
| `SING_7` | AIN7 vs AINCOM |

### Differential Inputs

| Constant | Channels |
|----------|----------|
| `DIFF_0_1` | AIN0(+) / AIN1(-) |
| `DIFF_2_3` | AIN2(+) / AIN3(-) |
| `DIFF_4_5` | AIN4(+) / AIN5(-) |
| `DIFF_6_7` | AIN6(+) / AIN7(-) |

## PGA Settings

| Constant | Gain | Input Range (VREF=2.5V) |
|----------|------|-------------------------|
| `PGA_1` | 1 | ±5.0 V |
| `PGA_2` | 2 | ±2.5 V |
| `PGA_4` | 4 | ±1.25 V |
| `PGA_8` | 8 | ±625 mV |
| `PGA_16` | 16 | ±312.5 mV |
| `PGA_32` | 32 | ±156.25 mV |
| `PGA_64` | 64 | ±78.125 mV |

## Data Rate Settings

| Constant | Rate |
|----------|------|
| `DRATE_30000SPS` | 30,000 SPS |
| `DRATE_15000SPS` | 15,000 SPS |
| `DRATE_7500SPS` | 7,500 SPS |
| `DRATE_3750SPS` | 3,750 SPS |
| `DRATE_2000SPS` | 2,000 SPS |
| `DRATE_1000SPS` | 1,000 SPS |
| `DRATE_500SPS` | 500 SPS |
| `DRATE_100SPS` | 100 SPS |
| `DRATE_60SPS` | 60 SPS |
| `DRATE_50SPS` | 50 SPS |
| `DRATE_30SPS` | 30 SPS |
| `DRATE_25SPS` | 25 SPS |
| `DRATE_15SPS` | 15 SPS |
| `DRATE_10SPS` | 10 SPS |
| `DRATE_5SPS` | 5 SPS |
| `DRATE_2SPS` | 2 SPS |

## Platform-Specific Notes

### ESP32

For ESP32, you can use VSPI (default) or HSPI:

```cpp
// VSPI (default)
ADS1256 adc(16, 17, ADS1256::PIN_UNUSED, 15, 2.500, &SPI);

// HSPI
SPIClass hspi(HSPI);
hspi.begin(14, 25, 13);  // SCK, MISO, MOSI
ADS1256 adc(16, 17, ADS1256::PIN_UNUSED, 15, 2.500, &hspi);
```

### RP2040

For RP2040, configure SPI pins before initialization:

```cpp
SPI.setSCK(2);
SPI.setTX(3);
SPI.setRX(4);
ADS1256 adc(7, ADS1256::PIN_UNUSED, 6, 5, 2.500, &SPI);
```

### Custom SPI Initialization

To prevent the library from calling `SPI.begin()`:

```cpp
#define ADS1256_SPI_ALREADY_STARTED
#include <ADS1256.h>
```

## Documentation

Full documentation available at:
- [PDF Documentation](extras/ADS1256_ArduinoLibrary_Documentation_20251023.pdf)
- [Blog Post](https://curiousscientist.tech/blog/ADS1256-custom-library)

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Author

**Curious Scientist**
- Website: [curiousscientist.tech](https://curiousscientist.tech)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Acknowledgments

Special thanks to:
- Abraão Queiroz - ESP32 corrections
- Benjamin Pelletier - DRDY signal handling fix
- RadoMmm - ADC-to-Volts conversion improvement
