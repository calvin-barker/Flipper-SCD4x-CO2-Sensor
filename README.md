# Flipper SCD4x CO2 sensor

A PoC Sensirion SCD4x CO2 sensor plugin for the Flipper Zero.    
This app is a WIP, the code is a bit of a mess and needs some more work, I just made the GUI functional enough to show the sensor readings (the included library should be fully functional though).    

![Screenshot](/images/flipper_screenshot.png)
## Connections
The connections are pretty straight-forward. Some boards have different form factors, but usually all have i2c (SDA+SCL), just look for those labels.    

![Connections](/images/SCD4x_gpio_0.5x.png)

## Connections to the Flipper Zero Wi-Fi Module v1

The Wi-Fi Module V1 requires only **4 essential pins** for basic operation:

**Essential Pins:**
- **3.3V (Pin 9)**: Power supply to the Wi-Fi module
- **GND (Pin 11)**: Ground connection
- **Pin 13 (TX)** → **Wi-Fi Module Pin 13 (RX)**: Flipper TX connects to Module RX
- **Pin 14 (RX)** → **Wi-Fi Module Pin 14 (TX)**: Flipper RX connects to Module TX

**Note:** In UART communication, TX (transmit) always connects to RX (receive) and vice versa. The pin numbers happen to match (Pin 13 to Pin 13, Pin 14 to Pin 14), but the labels are crossed (TX to RX, RX to TX).

**Alternative Power Pins (if Pin 9 and Pin 11 are unavailable):**
- **5V (Pin 1)**: Alternative power supply - **requires enabling** via `GPIO` → `5V on GPIO` → `ON`
- **GND (Pin 8)**: Alternative ground connection

**⚠️ Important:** The Wi-Fi Module V1 has a built-in 3.3V converter, so using Pin 1 (5V) is safe and will be automatically regulated to 3.3V by the module. Make sure to enable the 5V output on Pin 1 via `GPIO` → `5V on GPIO` → `ON` before connecting.

**Wire Color Recommendations:**
Standard wiring color conventions for clarity:
- **Red**: Power (5V on Pin 1 or 3.3V on Pin 9)
- **Black**: Ground (GND on Pin 8 or Pin 11)
- **Yellow/Orange**: TX (Pin 13) - transmit line
- **Green**: RX (Pin 14) - receive line

For the SCD4x sensor (I2C connections):
- **Blue**: SDA (I2C data line)
- **White**: SCL (I2C clock line)
- **Red**: 3.3V power
- **Black**: GND

**Note:** Your SCD4x sensor uses I2C (SDA/SCL) on the external I2C bus, which uses different GPIO pins than the UART pins used by the Wi-Fi module. Therefore, both devices can coexist without pin conflicts.

**Alternative UART Pins (if needed):**
If you need to use different UART pins to avoid conflicts, you can configure the Flipper Zero to use:
- **TX: Pin 15**
- **RX: Pin 16**

To change the UART pins, navigate on your Flipper Zero: `GPIO` → `USB-UART Bridge` → `LEFT` → `UART Pins`

## Verifying Wi-Fi Module Operation

**LED Behavior:**
The Wi-Fi Module V1 has LED indicators that show its status during initialization. The LED sequence you observe indicates normal operation:

- **Blue LED**: Indicates CC1101 mode
- **Green LED**: Indicates Wi-Fi mode
- **Red LED**: Indicates 2.4GHz transceiver mode

**If you see: Blue → Green → Red → No Light:**
This is **normal startup behavior**. The module is cycling through its function modes during initialization. Once the LED turns off, the module has completed initialization and is ready for operation.

**Other LED Behaviors:**
- **Brief blue blink then off**: Simple power-up initialization (also normal)
- **Solid color**: Module is active in that specific mode
- **No LED activity**: Module may be in standby or ready state

**Ways to Verify:**

1. **Using ESP32 WiFi Marauder:**
   
   **Option A: Directly on Flipper Zero**
   - Navigate to `Applications` → `ESP32` → `WiFi Marauder` on your Flipper Zero
   - Launch the application to access the Marauder interface
   - Select `Scan` → `AP` (Access Points) to scan for nearby Wi-Fi networks
   - If the scan returns a list of available networks, your Wi-Fi Module is functioning correctly
   
   **Option B: Via USB-UART Bridge (CLI)**
   - Ensure your Wi-Fi Module has ESP32 WiFi Marauder firmware installed
   - On your Flipper Zero, navigate to `GPIO` → `USB-UART Bridge`
   - Set the baudrate to **115200**
   - Connect your Flipper Zero to your computer via USB
   - On your computer, open a serial terminal application (e.g., PuTTY, Arduino Serial Monitor, or similar)
   - Configure the terminal:
     - **Baudrate**: 115200
     - **Data Bits**: 8
     - **Parity**: None
     - **Stop Bits**: 1
     - **Port**: The COM/USB port assigned to your Flipper Zero
   - Open the connection
   - Press the reset button on your Wi-Fi Module (if available) or power cycle it
   - You should see the Marauder title screen appear in the terminal
   - Type `help` and press Enter - a list of available commands should display
   - Type `scan` to perform a Wi-Fi scan - if networks appear, your module is working correctly

2. **Test with Other Wi-Fi Applications:**
   - Install a Wi-Fi app like [FlipWiFi](https://github.com/jblanked/FlipWiFi) on your Flipper Zero
   - Launch the app and try to scan for Wi-Fi networks
   - If the app successfully detects the module and can scan networks, your module is working correctly

3. **Check Firmware Status:**
   - The Wi-Fi Module V1 requires appropriate firmware to function
   - If apps don't detect the module, you may need to flash the module with the correct firmware
   - For ESP32 WiFi Marauder firmware, refer to the official [ESP32 WiFi Marauder documentation](https://github.com/justcallmekoko/ESP32Marauder)
   - Refer to the official Flipper Zero Wi-Fi Module documentation for firmware installation instructions

4. **Function Switch (if applicable):**
   - Some Wi-Fi Module V1 boards have a function switch on the PCB
   - Ensure the switch is set to the desired mode (Wi-Fi mode typically uses the green LED)
   - The LED should illuminate in the corresponding color when set correctly

5. **Basic Troubleshooting:**
   - Verify all 4 connections are secure (power, ground, TX, RX)
   - If using Pin 1 (5V), ensure it's enabled via `GPIO` → `5V on GPIO` → `ON`
   - Check that the module's firmware is up to date
   - Try disconnecting and reconnecting the module

If the module shows the LED sequence (blue → green → red → off) but apps can't communicate with it, the issue is likely firmware-related rather than wiring.

## Contributions
Contributions are welcome!    
There are a few things already in the roadmap:
* A menu to configure some chip-specific options such as chip type (SCD40/41), low power mode, auto calibration, altitude, pressure, etc.
* A refactoring of the GUI app to generally improve the code style and to keep the status in a struct instead of global variables
## Credits
* The scd4x library is Sparkfun's [SparkFun SCD4x CO2 Sensor Library](https://github.com/sparkfun/SparkFun_SCD4x_Arduino_Library) which I modified a bit to adapt it to the Flipper.
* The basic app structure was adapted from https://github.com/Mywk/FlipperTemperatureSensor
* [@litui](https://twitter.com/litui) for the inspiration to make this
