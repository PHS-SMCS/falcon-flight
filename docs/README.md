# Hardware Docs

This page is for the hardware documentation of the Falcon Flight embedded controller board. For software documentation, visit the official [blimp-utils repository](https://github.com/Ballistyxx/blimp-utils), or the [ReadTheDocs page](https://Ballistyxx.github.io/blimp-utils/) directly.

---

<img src="/docs/FFB6.1_Schematic.svg" alt="Full Falcon Flight embedded controller board schematic" width="500" ALIGN="center" HSPACE="20" VSPACE="20"/>

## 🔧 Features

### DRV8212p Motor Drivers [(Datasheet)](./datasheets/DRV8212p.pdf)

This board uses four Texas Instruments DRV8212P motor-driver ICs (Integrated Circuits) for reliable bidirectional brushed DC motor driving. Each IC capable of handling up to 1020-size coreless motors, and has a WSON-8 form factor of 2.0 x 2.0 x 0.8 mm.

A data table for each motor driver connection is shown below:

<img src="/images/Raspberry_Pi_Pinout.webp" alt="Raspberry Pi Zero 2W pinout, as a visual aid to the table below" width="200" ALIGN="right" HSPACE="20" VSPACE="20"/>

| Motor | DRV8212p Pin | Raspberry Pi Pin |
|-------|--------------|------------------|
| 1     | IN1          | GPIO21           |
| 1     | IN2          | GPIO13           |
| 2     | IN1          | GPIO20           |
| 2     | IN2          | GPIO6            |
| 3     | IN1          | GPIO16           |
| 3     | IN2          | GPIO5            |
| 4     | IN1          | GPIO19           |
| 4     | IN2          | GPIO26           |

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

#### BMI270 [(Datasheet)](./datasheets/BMI270.pdf)

* Consumes ~850μA in full IMU mode
* ODR (Output Data Rate) of up to 6.4kHz
* Typical noise density: ~0.09 mg/√Hz (accel), ~0.007 °/s/√Hz (gyro)
* Package: 2.5 mm × 3.0 mm × 0.83 mm LGA-14
* Accelerometer has ±2g/±4g/±8g/±16g ranges
* Gyroscope has ±125dps/±250dps/±500dps/±1000dps/±2000dps ranges

**Supporting passives:** The BMI270 is supported by the following passives:
* A 10µF, 6.3v bypass capacitor, tied to 3v3 and GND.
* A 100kohm resistor, tying SDO to GND (to set I2C address).
* A 100kohm resistor, tying CSB to 3v3 (pulled high to VDDIO).
* 2x 100nF bypass capacitors, tying VDDIO to GND.


#### BMM350 [(Datasheet)](./datasheets/BMM350.pdf)

* Consumes ~180µA at 100 Hz
* True hard-iron + soft-iron compensation, factory calibrated
* Noise: ~20 nT/√Hz
* Package: 2.0 mm × 2.0 mm × 0.75 mm LGA-8
* Up to 100Hz sampling rate

**Supporting passives:** The BMM350 is supported by the following passives:
* A 45dB 120mA fixed 1.8v linear regulator (ME6206A18XG, [Datasheet](./datasheets/ME6206A18XG.pdf))
* 2x 2.2µF, 16v bypass capacitors tying 3v3 and 1v8 to GND, respectively.
* A 10µF, 6.3v bulk capacitor tying 1v8 to GND.
* A 100nF, 16v bypass capacitor tying 1v8 to GND.
* a 2.2µF, 16v bypass capacitor tying CRST to GND (used as offsite charge storage for `BMM350_magnetic_reset_and_wait`).

Since the Raspberry Pi Zero 2W only has weak (~50kohm - ~65kohm) software-configurable pullup resistors, the board comes with 2.2kohm pullup resistors on the SDA and SCL lines, for better signal integrity above 100kHz speeds.


### TP4057 Battery Charging [(Datasheet)](./datasheets/TP4057.pdf)

This board uses 2x TP4057 battery charging ICs, one for each battery. Each TP4057 is calibrated to charge at a maximum I<sub>prog</sub> of 200mA in hardware. This cannot be changed without physically altering the board.

* Charging terminates when I<sub>CHARGE</sub> is lower than 20mA (10% of I<sub>PROG</sub>)
* Idle current of 25μA
* Has built-in UVLO, trickle charging, and internal thermal regulation functionality
* Built using the linear regulator charging architecture
* Supports only 1S1P LiPo batteries.

**Supporting passives:** Each TP4057 is supported by the following passives:
* A 5.1kohm resistor tying R<sub>PROG</sub> to GND. This resistance roughly corresponds to a charging current of 200mA, calculated by the equation R<sub>PROG</sub> = 1000V / I<sub>BAT</sub>.
* A 10μF, 25v bypass capacitor bypassing the positive and negative terminals of the LiPo battery.
* A 120 mcd red LED, tied in series with a 1kohm resistor from VBUS to CHRG# (The TP4057's status indication outputs are open-drain.)
* A 260 mcd green LED, tied in series with a 10kohm resistor from VBUS to STDBY#.


The TP4057 indicates its charging status via two LEDs. A table of the LED behavior is below:

| State                 | Charge (Red) | Standby (Green) |
|-----------------------|--------------|------------------|
| Charging              | ON           | OFF              |
| Charging complete     | OFF          | ON               |
| No battery            | Flicker*     | ON               |

*About 20Hz.

The TP4057 ICs are powered by the USB-C port on the board. This USB-C port is USB Type-C 2.0 "Upstream Facing Port" (UFP) spec compliant, meaning that it will request default USB power (500mA from a USB 2.0 source, 900mA from a USB 3.0 source.) The USB-C port uses only two 5.1kohm resistors on the CC1 and CC2 lines, so only passive current advertisement occurs. Thus, please only use USB-C compliant chargers with this board, as voltages higher than 5v will damage the charging circuitry.

### MT3608 Boost Converter [(Datasheet)](./datasheets/MT3608.pdf)

This board uses the MT3608 Boost Converter IC, in conjunction with a 6.8µH inductor and a SS54 diode. The output voltage of the MT3608 is calibrated to 5v in hardware. this cannot be changed without physically altering the board.

* Input Voltage: 2.0v to 24v
* Output Current: 2A peak, 1.2A continuous. This is enough to power a Raspberry Pi Zero 2W under full load, running a Pi Camera V3 and a 1080p HDMI stream. Additional sensors connected to the 5V line will likely also be supported, as long as the total continous current does not exceed ~1.2A.
* Switching Frequency: ~1.2 MHz
* Efficiency: Up to 93%
* Package: SOT-23-6

**Supporting passives:** The MT3608 is supported by the following passives:
* A 6.8μH ferrite-core 5.0 x 5.0 x 2.0 mm SMD inductor (~74mohm DCR, I<sub>sat</sub>: 2.3A, I<sub>rms</sub>: 2.1A @ 100kHz, 0.1v)
* A SS54 DO-214AC SMD Schottky Diode
* A 220kohm / 30kohm resistor divider, dividing FB between 5v and GND. Using the specified equation, with internal reference VREF = 0.6v, V<sub>OUT</sub> = V<sub>REF</sub> × (1 + R<sub>1</sub>/R<sub>2</sub>) = ~5v.
* A 22μF 25v, 1μF 50v, and a 100nF 50v decoupling capacitor to filter and absorb switching noise. 

The MT3608 is connected to the Raspberry Pi battery via the DMP2035U-7 P-Channel Power MOSFET [(Datasheet)](./datasheets/DMP2035U-7.pdf), controlled by the power switch. This design choice was implemented to relieve the strain of passing high current through the power switch in 'analog mode', and to allow for a compact power switch when turning on two batteries.

### ESD Protection

The Falcon Flight board includes a P6SMB6.8CA TVS diode on the 5V rail before any current enters the rest of the board. In the case of an ESD event (>10.5v), this diode will clamp the incoming voltage to GND before any sensitive electronics recieve prolonged exposure.
