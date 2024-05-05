# SUP Console Flash Programmer
## Programmer Source Code and Compiled Firmware

## Compiled Firmware
In the `Compiled_HEX` directory, there are already precompiled firmware files for the programmer. They simply need to be flashed into the memory of the Arduino Mega, and there is no need to change the fuse configuration. The firmware name is formed as follows:

```sh
<firmware_name>_<baud_rate>_<maximum_data_block_size>_<microcontroller_clock_frequency>.hex
hex
```
Attention! `<baud_rate>` must match the COM port operating speed.

## Programmer Source Code
Located in the `Source` directory
The source code is written in C language using the IAR Embedded Workbench for Microchip AVR 7.30.4 development environment and is saved as a project.