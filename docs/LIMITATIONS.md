# Limitations and Known Constraints

## Revision Status

This is a **Revision 1 engineering prototype**. A clean schematic DRC and PCB DRC do not prove that the physical board will work. Final validation requires assembly and testing.

## Electrical Limitations

### 3.3 V Only

The board is designed for a **3.3 V supply**.

Do not apply 5 V to the 3V3 header pin.

There is no onboard regulator and no input over-voltage protection.

### I2C Pull-Ups

R1 and R2 are 10 kOhm.

This is a good low-current starting value for:

- short wires
- low bus capacitance
- initial 100 kHz I2C operation

For longer cables, higher bus capacitance, or faster I2C operation, lower values such as 4.7 kOhm may be more appropriate.

### No Level Shifting

SDA and SCL are referenced to 3.3 V. The external controller should use 3.3 V-compatible I/O.

### No Reverse-Polarity Protection

Reversing 3V3 and GND may damage U1.

### No ESD / Surge Protection

The external connector does not include TVS diodes or dedicated ESD protection.

This board is intended for bench, student, prototype, and embedded-development use rather than harsh industrial environments.

## Mechanical Limitations

### MEMS Stress Sensitivity

IMUs can be affected by:

- PCB bending
- mechanical stress
- vibration
- mounting pressure
- temperature
- soldering stress

The sensor should not be mechanically loaded by screws or enclosure pressure.

### Mounting Holes

Mounting hardware should not flex the PCB. Use spacers/standoffs and avoid over-tightening screws.

## Manufacturing Limitations

### Hidden LGA Solder Joints

The ICM-42670-P has solder joints under the package. Visual inspection alone cannot guarantee correct assembly.

### Footprint and Orientation

A correct schematic can still fail if:

- the footprint is wrong
- pin 1 is rotated incorrectly
- the CPL rotation is wrong
- the package is misplaced

U1 orientation must be checked in the final assembly preview.

### Component Availability

The listed LCSC/JLCPCB part numbers are manufacturing choices, not permanent guarantees. Stock status and library classification can change.

If substitutions are made, verify:

- value
- tolerance
- voltage rating
- dielectric
- package
- pinout
- manufacturer specifications

## Firmware Limitations

This repository currently documents the hardware design and test plan. A complete production firmware driver is not yet part of the validated hardware release.

Initial firmware should:

1. use 3.3 V logic
2. start at 100 kHz I2C
3. scan for 0x68
4. verify the device identification register
5. configure the accelerometer and gyroscope
6. verify changing measurements while moving the PCB

## Calculation Limitations

The current and power calculations in the README are design estimates based on nominal values and documented typical operating current.

Real current can vary with:

- operating mode
- output data rate
- gyroscope configuration
- accelerometer configuration
- temperature
- bus activity
- interrupt configuration
- process variation

The conservative 1.54 mA calculation is **not an absolute maximum rating**. It is a simple engineering scenario that adds the typical IMU current to three possible 0.33 mA resistor currents.

Always use the latest official datasheet for absolute maximum ratings and guaranteed electrical limits.

## Validation Still Required

Before calling this design proven, Revision 1 should pass:

- continuity and short-circuit inspection
- 3V3-to-GND resistance check before power
- controlled first power-up
- measured supply current
- I2C address scan
- WHO_AM_I / device-ID verification
- accelerometer response test
- gyroscope response test
- interrupt test
- repeated power cycling
- basic temperature and motion repeatability checks

## Intended Use

This is a learning/prototype board. It is not certified for:

- medical use
- automotive use
- aerospace flight hardware
- life-safety systems
- industrial functional-safety systems
- hazardous environments
