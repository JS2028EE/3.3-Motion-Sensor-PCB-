# 3.3 V Motion Sensor PCB — ICM-42670-P

This repository documents my **first custom PCB circuit**, designed in EasyEDA as a compact 3.3 V motion-sensor / IMU module based on the **TDK InvenSense ICM-42670-P**.

The board is intended to connect directly to a 3.3 V microcontroller such as an ESP32 over I²C and provide 3-axis acceleration, 3-axis gyroscope data, an interrupt output, and external I²C address control.

---

## Project Purpose

The purpose of this project is to learn the complete PCB design process:

- reading a sensor datasheet
- selecting support components
- designing the schematic
- calculating voltage, current, and power
- creating a PCB layout
- routing a 2-layer PCB
- adding a ground copper plane
- checking DRC
- preparing the board for fabrication and assembly

The ICM-42670-P is the main sensor. It combines a 3-axis accelerometer and a 3-axis gyroscope into one 6-axis IMU.

---

## Electrical Design

### Supply Voltage

The PCB is designed for:

```
3.3 V DC
```

The 3.3 V rail connects to:

- U1 pin 8 — VDD
- U1 pin 5 — VDDIO
- U1 pin 12 — AP_CS
- R1 — SCL pull-up
- R2 — SDA pull-up
- H1 pin 1 — 3V3 input

Ground connects to:

- U1 pin 6 — GND
- U1 pin 7 — FSYNC
- C1, C2, C3 ground sides
- R3 ground side
- H1 pin 2 — GND

This board must be powered from **3.3 V, not 5 V**.

---

## Header Pinout

| Header Pin | Signal | Function |
|---:|---|---|
| 1 | 3V3 | 3.3 V power input |
| 2 | GND | Ground |
| 3 | SCL | I²C clock |
| 4 | SDA | I²C data |
| 5 | INT | ICM-42670-P interrupt output |
| 6 | AD0 | I²C address select |

Typical ESP32 connection:

```
PCB H1-1 3V3 -> ESP32 3V3
PCB H1-2 GND -> ESP32 GND
PCB H1-3 SCL -> ESP32 I2C SCL GPIO
PCB H1-4 SDA -> ESP32 I2C SDA GPIO
PCB H1-5 INT -> Optional ESP32 GPIO
PCB H1-6 AD0 -> Optional external address control
```

---

## Main Components

| Reference | Component | Value / Part | Purpose |
|---|---|---|---|
| U1 | 6-axis IMU | ICM-42670-P | Measures acceleration and angular velocity |
| R1 | Resistor | 10 kΩ | SCL pull-up |
| R2 | Resistor | 10 kΩ | SDA pull-up |
| R3 | Resistor | 10 kΩ | AD0 pull-down |
| C1 | Ceramic capacitor | 100 nF | VDD high-frequency decoupling |
| C2 | Ceramic capacitor | 2.2 µF | VDD bulk/local decoupling |
| C3 | Ceramic capacitor | 10 nF | VDDIO decoupling |
| H1 | 1x6 header | 2.54 mm pitch | Power and signal interface |

### JLCPCB / LCSC Parts Used

| Reference | LCSC Part |
|---|---|
| C1 | C1591 |
| C2 | C165991 |
| C3 | C1589 |
| R1, R2, R3 | C25804 |
| U1 | C3288646 |
| H1 | C46061679 |

---

# Electrical Calculations

## 1. ICM-42670-P Power

For the design calculations, a typical full 6-axis operating current of approximately:

```
I = 0.55 mA
```

is used.

Using:

```
P = V x I
```

```
P = 3.3 V x 0.55 mA
P = 3.3 x 0.00055
P = 0.001815 W
```

Therefore:

```
P(U1) ≈ 1.82 mW
```

So the sensor's approximate operating point used for this design estimate is:

```
Voltage: 3.3 V
Current: 0.55 mA
Power:   1.82 mW
```

---

## 2. R1 — SCL Pull-Up

```
R1 = 10 kΩ
```

R1 connects from 3.3 V to SCL.

When SCL is pulled LOW, the maximum current through R1 is:

```
I = V / R
I = 3.3 / 10000
I = 0.00033 A
I = 0.33 mA
```

Maximum resistor power:

```
P = V² / R
P = 3.3² / 10000
P = 10.89 / 10000
P = 0.001089 W
```

Therefore:

```
I(R1,max) = 0.33 mA
P(R1,max) = 1.089 mW
```

For a 100 mW resistor:

```
1.089 / 100 x 100% ≈ 1.09%
```

R1 therefore operates at only about **1.09% of a 100 mW rating** under this worst static condition.

---

## 3. R2 — SDA Pull-Up

R2 is also:

```
R2 = 10 kΩ
```

When SDA is LOW:

```
I = 3.3 / 10000
I = 0.33 mA
```

Power:

```
P = 3.3² / 10000
P = 1.089 mW
```

Therefore:

```
I(R2,max) = 0.33 mA
P(R2,max) = 1.089 mW
```

---

## 4. R3 — AD0 Pull-Down

R3 is:

```
R3 = 10 kΩ
```

It holds AD0 LOW when nothing else drives the pin.

With AD0 LOW, both ends of R3 are approximately at 0 V:

```
I ≈ 0 A
P ≈ 0 W
```

If AD0 is externally driven HIGH to 3.3 V:

```
I = 3.3 / 10000
I = 0.33 mA
```

and:

```
P = 3.3² / 10000
P = 1.089 mW
```

So the maximum calculated load for R3 in that condition is:

```
I(R3,max) = 0.33 mA
P(R3,max) = 1.089 mW
```

The default I²C address is:

```
AD0 = LOW  -> 0x68
```

The alternate address is:

```
AD0 = HIGH -> 0x69
```

---

## 5. C1 — 100 nF VDD Decoupling

```
C1 = 100 nF
V  = 3.3 V
```

Charge:

```
Q = C x V
Q = 100 nF x 3.3 V
Q = 0.33 µC
```

Stored energy:

```
E = 1/2 C V²
E = 0.5 x 100e-9 x 3.3²
E ≈ 0.545 µJ
```

Therefore:

```
Q(C1) ≈ 0.33 µC
E(C1) ≈ 0.545 µJ
```

C1 is the fast local decoupling capacitor for VDD.

---

## 6. C2 — 2.2 µF VDD Decoupling

```
C2 = 2.2 µF
V  = 3.3 V
```

Charge:

```
Q = C x V
Q = 2.2 µF x 3.3 V
Q = 7.26 µC
```

Stored energy:

```
E = 1/2 C V²
E = 0.5 x 2.2e-6 x 3.3²
E ≈ 11.98 µJ
```

Therefore:

```
Q(C2) = 7.26 µC
E(C2) ≈ 11.98 µJ
```

C2 acts as a larger local energy reserve on the VDD rail.

---

## 7. C3 — 10 nF VDDIO Decoupling

```
C3 = 10 nF
V  = 3.3 V
```

Charge:

```
Q = C x V
Q = 10 nF x 3.3 V
Q = 0.033 µC
```

Stored energy:

```
E = 1/2 C V²
E = 0.5 x 10e-9 x 3.3²
E ≈ 0.0545 µJ
```

Therefore:

```
Q(C3) ≈ 0.033 µC
E(C3) ≈ 0.0545 µJ
```

C3 provides local VDDIO decoupling.

---

## 8. Capacitor Voltage Margin

The board operates at:

```
3.3 V
```

Selected capacitor voltage ratings:

```
C1: 50 V
C2: 10 V
C3: 50 V
```

C2 voltage margin:

```
10 / 3.3 ≈ 3.03x
```

C1 and C3 voltage margin:

```
50 / 3.3 ≈ 15.15x
```

All selected capacitors therefore have voltage ratings comfortably above the 3.3 V operating rail.

---

## 9. Normal Board Current

With SDA and SCL HIGH most of the time, the main continuous load is approximately the IMU:

```
I(board) ≈ 0.55 mA
```

Power:

```
P = 3.3 x 0.00055
P ≈ 1.82 mW
```

---

## 10. Both I²C Lines LOW

Each 10 kΩ pull-up contributes:

```
0.33 mA
```

Therefore:

```
I(total) = 0.55 + 0.33 + 0.33
I(total) ≈ 1.21 mA
```

Power:

```
P = 3.3 x 0.00121
P ≈ 3.99 mW
```

So with both SDA and SCL held LOW:

```
Current ≈ 1.21 mA
Power   ≈ 3.99 mW
```

---

## 11. Conservative High-Current Condition

For a conservative calculation, assume:

- the IMU is operating normally
- SDA is LOW
- SCL is LOW
- AD0 is externally driven HIGH against R3

Then:

```
I(total) = 0.55 + 0.33 + 0.33 + 0.33
I(total) ≈ 1.54 mA
```

Power:

```
P = 3.3 x 0.00154
P ≈ 5.08 mW
```

Therefore:

```
Conservative calculated current ≈ 1.54 mA
Conservative calculated power   ≈ 5.08 mW
```

---

## 12. Recommended Power Supply Capability

The calculated current requirement is very small.

A reasonable design allowance is:

```
5 to 10 mA minimum available at 3.3 V
```

A typical ESP32 development board's 3.3 V regulator can supply far more than this sensor board requires.

---

## 13. I²C Pull-Up and Rise-Time Estimate

For the 10 kΩ SDA and SCL pull-ups:

```
R = 10 kΩ
```

Approximate I²C rise time can be estimated with:

```
tr ≈ 0.8473 R C
```

For approximately 1 µs rise time at 100 kHz:

```
Cmax = 1e-6 / (0.8473 x 10000)
Cmax ≈ 118 pF
```

So a short PCB and short external wiring should normally be suitable for initial **100 kHz I²C** operation.

For faster buses or longer wiring, lower pull-up values such as 4.7 kΩ may be considered.

At 4.7 kΩ:

```
I = 3.3 / 4700
I ≈ 0.702 mA
```

which is still a very small current.

---

# Complete Electrical Summary

| Parameter | Design / Calculated Value |
|---|---:|
| Supply voltage | 3.3 V DC |
| Typical IMU current used for calculations | ≈ 0.55 mA |
| Typical IMU power estimate | ≈ 1.82 mW |
| R1 | 10 kΩ |
| R2 | 10 kΩ |
| R3 | 10 kΩ |
| Maximum current through each 10 kΩ resistor at 3.3 V | 0.33 mA |
| Maximum resistor dissipation | 1.089 mW |
| C1 | 100 nF |
| C2 | 2.2 µF |
| C3 | 10 nF |
| Both I²C lines LOW board current estimate | ≈ 1.21 mA |
| Both I²C lines LOW power estimate | ≈ 3.99 mW |
| Conservative calculated board current | ≈ 1.54 mA |
| Conservative calculated board power | ≈ 5.08 mW |
| Recommended supply capability | ≥ 5–10 mA at 3.3 V |
| Default I²C address | 0x68 |
| Alternate I²C address | 0x69 |
| Recommended initial I²C speed | 100 kHz |

---

# PCB Design

The PCB is a 2-layer design.

Important layout choices:

- ICM-42670-P placed near the center
- C1 100 nF placed close to VDD
- C2 2.2 µF placed near VDD
- C3 10 nF placed close to VDDIO
- R1 and R2 placed near the I²C signal area
- R3 placed near AD0
- 1x6 header positioned along the board edge
- Bottom layer uses a GND copper pour
- Signal routing uses both top and bottom layers where necessary
- DRC completed with no reported errors before manufacturing review

Approximate board dimensions:

```
24.99 mm x 17.83 mm
```

The design is intended for standard 2-layer FR-4 fabrication.

---

# Manufacturing / Assembly

The board was prepared for JLCPCB fabrication and PCBA.

Matched assembly parts included:

```
C1  -> C1591
C2  -> C165991
C3  -> C1589
R1/R2/R3 -> C25804
U1  -> C3288646
H1  -> C46061679
```

The ICM-42670-P is an LGA device with hidden solder joints underneath the package, so professional assembly is useful for the first revision.

---

# First Power-On Test Plan

Before connecting the board to an ESP32:

1. Inspect the assembled PCB for orientation and visible damage.
2. Confirm there is no short between 3V3 and GND.
3. Apply regulated 3.3 V power.
4. Measure board current.
5. Confirm the 3.3 V rail remains stable.
6. Connect SDA and SCL to the ESP32.
7. Start with 100 kHz I²C.
8. Run an I²C scanner.
9. Look for address `0x68`.
10. Read the ICM-42670-P identification register.
11. Initialize the accelerometer and gyroscope.
12. Verify that sensor values change when the board is moved and rotated.
13. Test INT if interrupt operation is needed.

If AD0 is externally driven HIGH, scan for `0x69`.

---

# Engineering Conclusion

This design is a very low-power 3.3 V custom IMU module. The 10 kΩ resistors operate far below their power ratings, the selected capacitors have substantial voltage margin, and the estimated operating current is small enough to be comfortably powered from a typical ESP32 3.3 V rail.

The calculated conservative board load is only about **1.54 mA / 5.08 mW** under the specific high-current condition analyzed above. Normal operating consumption should be lower.

This is the first revision of the project and should be treated as an engineering prototype. Final validation occurs only after physical assembly, power-on testing, I²C communication, and real sensor measurements.
