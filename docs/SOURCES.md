# Sources and Documentation

The design should always be checked against the **latest official manufacturer documentation** before a new revision is released.

## Primary Device Sources

### TDK InvenSense ICM-42670-P Product Page

Official product page for the sensor, including current product status, package information, specifications, documentation, and software resources.

https://www.invensense.tdk.com/en-us/products/6-axis/icm-42670-p

Key information used from TDK documentation:

- 6-axis accelerometer + gyroscope
- 0.55 mA low-noise 6-axis current figure
- 1.71 V to 3.6 V supply range
- I2C / SPI / I3C interface support
- 2.5 mm x 3.0 mm 14-pin LGA package

### ICM-42670-P Datasheet — DS-000451

Official TDK datasheet:

https://invensense.tdk.com/wp-content/uploads/2021/07/ds-000451_icm-42670-p-datasheet.pdf

Used for:

- pin functions
- power-supply limits
- I2C configuration
- AP_CS behavior
- AD0 address behavior
- bypass capacitor requirements
- reserved-pin guidance
- electrical characteristics
- package information

### TDK ICM-42670-P Product Documentation Library

The TDK product page also lists related application notes, including PCB and MEMS assembly guidance. These should be reviewed when changing the mechanical layout or assembly method.

## EasyEDA Documentation

### EasyEDA Pro User Guide

https://prodocs.easyeda.com/

Used for:

- schematic/PCB workflow
- board-outline creation
- routing
- DRC
- copper region setup

### EasyEDA Pro Copper Region Documentation

https://prodocs.easyeda.com/en/pcb/place-copper-region/

Used for the bottom-layer GND copper pour.

### EasyEDA Pro Board Outline Documentation

https://prodocs.easyeda.com/en/pcb/place-board-outline/

Used for board-outline setup.

## JLCPCB Assembly Documentation

### BOM Requirements

https://jlcpcb.com/help/article/bill-of-materials-for-pcb-assembly

The BOM identifies parts, values, packages, and designators for assembly.

### Pick-and-Place / CPL Requirements

https://jlcpcb.com/help/article/pick-place-file-for-pcb-assembly

The CPL provides:

- designator
- X/Y location
- board side
- rotation

These values are critical for correct U1 orientation.

### PCB Assembly FAQ

https://jlcpcb.com/help/article/pcb-assembly-faqs

Useful for understanding:

- Basic vs Extended parts
- feeder-loading charges
- assembly requirements
- required manufacturing files

## Open Hardware Licensing

This hardware project uses the **CERN Open Hardware Licence Version 2 - Permissive (CERN-OHL-P-2.0)**.

Official Open Hardware Repository licensing page:

https://ohwr.org/licences/

SPDX identifier:

https://spdx.org/licenses/CERN-OHL-P-2.0.html

## Important Source-Control Rule

Third-party datasheets, manufacturer logos, proprietary component models, and vendor documentation are **not relicensed by this repository**. They remain the property of their respective owners.

This repository links to official documentation rather than claiming ownership of it.
