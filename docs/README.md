# Hardware Docs

This page is for the hardware documentation of the Falcon Flight embedded controller board. For software documentation, visit the official [blimp-utils repository](https://github.com/Ballistyxx/blimp-utils), or the [ReadTheDocs page](Ballistyxx.github.io/blimp-utils/) directly.

---

## 🔧 Features

### DRV8212p Motor Drivers [(Datasheet)]()

This board uses four Texas Instruments DRV8212P motor-driver ICs (Integrated Circuits) for reliable bidirectional brushed DC motor driving. Each IC capable of handling up to 1020-size coreless motors, and has a WSON-8 form factor of 2.0 x 2.0 x 0.8 mm.

A data table for each motor driver connection is shown below:

<img src="/images/Raspberry_Pi_Pinout.webp" alt="Raspberry Pi Zero 2W pinout, as a visual aid to the table below" width="200" ALIGN="right" HSPACE="20" VSPACE="20"/>

| Motor | DRV8212p Pin | Raspberry Pi Pin |
|-------|--------------|------------------|
| 1     | IN1          | 21               |
| 1     | IN2          | 13               |
| 2     | IN1          | 20               |
| 2     | IN2          | 6                |
| 3     | IN1          | 16               |
| 3     | IN2          | 5                |
| 4     | IN1          | 19               |
| 4     | IN2          | 26               |

**Supporting passives:** Each DRV8212p is supported by the following passives:
* A 22μF, 25V MLCC bulk capacitor, tied to VM and GND
* A 100nF, 50V MLCC bypass capacitor, tied to VM and GND
* A 100nF, 50V MLCC bypass capacitor, tied to VCC and GND
* A 10kohm resistor, pulling nSleep `HIGH`. I elected to always keep each motor driver on, rather than allow them to be turned off in software to eliminate complexity and points of failure. According to the datasheet, the DRV8212p only draws ~10mA when idle, which makes it negligible for our purposes.
* 2x 330ohm resistors in series with the INx pins. These protect the Raspberry Pi Zero 2W's GPIO pins from damage if an internal short occurs in the motor driver IC.

All DRV8212p motor driver ICs are heatsinked by the PCB. They are connected to a large ground plane shared by the top and bottom ground pours, as well as the internal GND layer, with at least 10 vias per driver. Under normal operating circumstances, heat dissipation should not be an issue.

At an ambient temperature of 25°C, each motor can be run at up to 1.5A continuously without exceeding the industry standard 10°C temperature rise. Short bursts of power above 1.5A are permissible. Continuous current of 2A comes close to the dangerous threshhold of thermal runaway at a 20°C temperature rise, and motors should not be allowed to draw more than 2A continously under any circumstances.

### BMI270 + BMM350 Sensor Suite

This board uses the BMI270 IMU and BMM350 magnetometer to gather triaxial accelerometer, gyroscope, and magnetometer data. Each sensor is pre-connected via I2C, with I2C addresses of 0x68 and 0x14, respectively. These I2C addresses are preconfigured in hardware, and are not possible to change without physically altering the board.

---

The datasheets for the BMI270 and BMM350 are incredibly long and teedious to pick information out of, so I have pulled the most important information to display here.

#### BMI270 [(Datasheet)]()

* Consumes ~850μA in full IMU mode
* ODR (Output Data Rate) of up to 6.4kHz
* Typical noise density: ~0.09 mg/√Hz (accel), ~0.007 °/s/√Hz (gyro)
* Package: 2.5 mm × 3.0 mm × 0.83 mm LGA-14
* Accelerometer has ±2g/±4g/±8g/±16g ranges
* Gyroscope has ±125dps/±250dps/±500dps/±1000dps/±2000dps ranges

#### BMM350 [(Datasheet)]()

* Consumes ~180µA at 100 Hz
* True hard-iron + soft-iron compensation, factory calibrated
* Noise: ~20 nT/√Hz
* Package: 2.0 mm × 2.0 mm × 0.75 mm LGA-8
* Up to 100Hz sampling rate

### TP4057 Battery Charging [(Datasheet)]()

This board uses 2x TP4057 battery charging ICs, one for each battery. Each TP4057 is calibrated to charge at a maximum I<sub>prog</sub> of 200mA in hardware. This cannot be changed without physically altering the board.

* Charging terminates when I<sub>charge</sub> is lower than 20mA (10% of I<sub>prog</sub>)
* Idle current of 25μA
* Has built-in UVLO, trickle charging, and internal thermal regulation functionality
* Built using the linear regulator charging architecture
* Supports only 1S1P LiPo batteries.

The TP4057 indicates its charging status via two LEDs. A table of the LED behavior is below:

| State                 | Charge (Red) | Standby (Green) |
|-----------------------|--------------|------------------|
| Charging              | ON           | OFF              |
| Charging complete     | OFF          | ON               |
| No battery            | Flicker*     | ON               |

*About 20Hz.

The TP4057 ICs are powered by the USB-C port on the board. This USB-C port is USB Type-C 2.0 "Upstream Facing Port" (UFP) spec compliant, meaning that it will request default USB power (500mA from a USB 2.0 source, 900mA from a USB 3.0 source.) The USB-C port uses only two 5.1kohm resistors on the CC1 and CC2 lines, so only passive current advertisement occurs. Thus, please only use USB-C compliant chargers with this board, as voltages higher than 5v will damage the charging circuitry.

### MT3608 Boost Converter [(Datasheet)]()

This board uses the MT3608 Boost Converter IC, in conjunction with a 6.8µH inductor and a SS54 diode. The output voltage of the MT3608 is calibrated to 5v in hardware. this cannot be changed without physically altering the board.

* Input Voltage: 2.0v to 24v
* Output Current: 2A peak, 1.2A continuous. This is enough to power a Raspberry Pi Zero 2W under full load, running a Pi Camera V3 and a 1080p HDMI stream. Additional sensors connected to the 5V line will likely also be supported, as long as the total continous current does not exceed ~1.2A.
* Switching Frequency: ~1.2 MHz
* Efficiency: Up to 93%
* Package: SOT-23-6

The MT3608 is connected to the Raspberry Pi battery via the DMP2035U-7 P-Channel Power MOSFET, controlled by the power switch. This design choice was implemented to relieve the strain of passing high current through the power switch in 'analog mode', and to allow for a compact power switch when turning on two batteries.

* **Extensive Bypass & Bulk Capacitance**: MLCCs on every IC ensure stability.

### ESD Protection

The Falcon Flight board includes a P6SMB6.8CA TVS diode on the 5V rail before any current enters the rest of the board. In the case of an ESD event (>10.5v), this diode will clamp the incoming voltage to GND before any sensitive electronics recieve prolonged exposure.
