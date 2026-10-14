# Design Rationale

## Author

**Jhostin ("Jay")**  
GitHub: **@JS2028EE**

This project is my first custom PCB design. It was created as a learning project to move from using ready-made sensor modules to designing the support circuitry, PCB layout, manufacturing files, and validation plan myself.

## Why the ICM-42670-P

The ICM-42670-P was chosen because it is a modern 6-axis IMU that combines:

- a 3-axis accelerometer
- a 3-axis gyroscope
- interrupt outputs
- I2C, SPI, and I3C host interfaces
- low-power operating modes

The project uses **I2C** because it requires only two communication lines, is simple to interface with an ESP32, and is appropriate for a small sensor breakout board.

The board is intentionally powered from **3.3 V** so it can connect directly to common 3.3 V microcontrollers without level shifting.

## Why Each Part Was Chosen

### U1 — ICM-42670-P

**Part:** ICM-42670-P  
**JLCPCB/LCSC:** C3288646  
**Package:** 14-pin LGA, 2.5 mm x 3.0 mm

Purpose:

- measures acceleration on X, Y, and Z axes
- measures angular velocity around X, Y, and Z axes
- provides the main sensing function of the PCB

Why chosen:

- current-production 6-axis IMU
- low power
- small package
- supported by official TDK documentation
- supports I2C for easy ESP32 connection
- suitable for robotics, embedded systems, motion detection, and orientation projects

### R1 — 10 kOhm SCL Pull-Up

**Part:** 10 kOhm 0603 resistor  
**LCSC:** C25804

Purpose:

- pulls the I2C SCL line HIGH when no device is actively pulling it LOW

Why chosen:

I2C uses open-drain signaling. SCL therefore requires an external pull-up resistor. A 10 kOhm value gives low current draw and is appropriate as an initial value for a short 100 kHz I2C bus.

At 3.3 V:

```
I = V / R
I = 3.3 / 10000
I = 0.33 mA
```

### R2 — 10 kOhm SDA Pull-Up

**Part:** 10 kOhm 0603 resistor  
**LCSC:** C25804

Purpose:

- pulls the I2C SDA line HIGH when no device is pulling it LOW

Why chosen:

SDA is also open-drain. The same 10 kOhm value is used for symmetry, low power, and compatibility with a short 100 kHz I2C bus.

### R3 — 10 kOhm AD0 Pull-Down

**Part:** 10 kOhm 0603 resistor  
**LCSC:** C25804

Purpose:

- gives AD0 a defined LOW state when the external header pin is left unconnected
- selects the default I2C address

Default state:

```
AD0 = LOW -> 0x68
```

If driven HIGH:

```
AD0 = HIGH -> 0x69
```

Why chosen:

Without R3, AD0 could float. A 10 kOhm pull-down defines the default address while still allowing an external controller to drive AD0 HIGH.

### C1 — 100 nF VDD Decoupling

**Part:** 100 nF X7R 0603 ceramic capacitor  
**LCSC:** C1591

Purpose:

- high-frequency local bypass capacitor for VDD
- supplies very short current transients close to U1
- reduces supply noise

Why chosen:

100 nF is the local high-frequency VDD bypass value used in the device reference circuitry. It is placed as close as practical to the VDD pin.

### C2 — 2.2 uF VDD Decoupling / Local Energy Storage

**Part:** 2.2 uF X7R 0603 ceramic capacitor  
**LCSC:** C165991

Purpose:

- provides larger local energy storage on VDD
- supports lower-frequency supply transients
- works together with C1

Why chosen:

The 2.2 uF capacitor complements the 100 nF capacitor by supporting larger and slower supply changes.

### C3 — 10 nF VDDIO Decoupling

**Part:** 10 nF X7R 0603 ceramic capacitor  
**LCSC:** C1589

Purpose:

- bypasses the VDDIO supply
- reduces digital-interface noise close to the sensor

Why chosen:

This value follows the ICM-42670-P support circuitry for VDDIO.

### H1 — 1x6 2.54 mm Header

**Part:** 1x6 2.54 mm header  
**JLCPCB/LCSC:** C46061679

Purpose:

Provides a simple external interface:

| Pin | Signal |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | SCL |
| 4 | SDA |
| 5 | INT |
| 6 | AD0 |

Why chosen:

- easy to connect with jumper wires
- breadboard-friendly 2.54 mm spacing
- exposes the minimum signals needed to power, communicate with, and configure the board

## Why a Two-Layer PCB

A two-layer PCB was chosen because it is inexpensive, easy to manufacture, and sufficient for this low-speed sensor interface.

The design uses:

- top layer for most component placement and signal routing
- bottom layer as a large GND copper plane with limited signal routing where required

The GND plane helps reduce return-path impedance and keeps the layout cleaner.

## Why the Sensor Is Near the Center

MEMS inertial sensors are sensitive to mechanical stress and board flex. Keeping the sensor away from the board edge and mounting holes reduces mechanical influence and also provides cleaner routing around the device.

## Why Professional Assembly Was Considered

The ICM-42670-P uses a small LGA package with solder pads underneath the body. These joints cannot be directly inspected visually after soldering.

For the first revision, professional SMT assembly reduces the risk of:

- incomplete hidden solder joints
- excessive solder
- bridging
- incorrect package placement
- thermal damage during manual rework

## Design Philosophy

This first revision is intentionally simple.

It does not include:

- an onboard voltage regulator
- 5 V input support
- level shifting
- onboard microcontroller
- USB
- SPI header breakout
- status LEDs
- ESD protection on external signals

The goal of Revision 1 is to validate the IMU, power network, I2C interface, PCB layout, and manufacturing workflow before adding more features.
