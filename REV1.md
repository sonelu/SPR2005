# Robotics HAT Revision A.1

The following picture shows the rev A.1 of the Robotics HAT and provides more details
about the components.

<img src="./imgs/rev1-anno.jpg?raw=true" width="800px">

## Power

The HAT can be powered from an external power source (using the JST-XH connector on the
lower left side) or through the Dynamixel Bus from the LiPo batteries powering the
robot. The typical usage is based on two separate batterries located in the feet of the
robot that power the Dyanamixel bus through a hot-swap circuit (thus allowing the change
of one battery at a time without the need to power down the main controller). Alternativelly
an external power source can be connected to the JST-XH connector and this will power
the Dynamixel and the DC-DC switching convertor for Raspberry Pi.

The separation between the supplies is performed with two sets of 'ideal' diodes as
described [here](https://github.com/raspberrypi/hats/blob/master/zvd-circuit.png): one
of these between the external power supply and the Dyanamixel bus and one between the
output of the DC-DC switch and the 5V input of the Raspberry Pi on the pin header.

The switching is provided by the high-performance [TPS82130SILT](https://www.ti.com/store/ti/en/p/product/?p=TPS82130SILT)
chip from Texas Instruments providing 3A max output, sufficient for the powering of
the Raspberry Pi and the 2 x 1 W audio output from the WM8960 chip.

## Audio

The HAT uses a [WM8960](https://www.cirrus.com/products/wm8960/)
chip that provides input/output codec for two omnidirectional
microphones placed on the edges of the board and can power 2 1W speakers that can
be connected using the two JST PH connectors.

The design is inspired by the [Waveshare Audio HAT](https://www.waveshare.com/wm8960-audio-hat.htm)
and the Seeedstudio's [ReSpeaker 2-Mics Pi HAT](https://www.seeedstudio.com/ReSpeaker-2-Mics-Pi-HAT.html).
For the time being the installation scripts suggested by these repositories are used to setup the
drivers and configure the chip.

The codec has its own 3.3V power supply for AVDD rail using a TLV70033 LDO.

We use high quality omnidirectional MEMS microphones 
[3729AT](https://www.cuidevices.com/product/audio/microphones/mems-microphones/cmm-3729at-42316-tr).

## Dynamixel Buses

THe HAT uses a dual UART interface [SC16IS752](https://www.nxp.com/products/peripherals-and-logic/signal-chain/bridges/dual-uart-with-ic-bus-spi-interface-64-bytes-of-transmit-and-receive-fifos-irda-sir-built-in-support:SC16IS752_SC16IS762) 
that is connected over SPI (CE1). These chips are now supported standard by the Linux kernel and
are nowadays relatively easy to use. The chip is 5V tolerant on the TX, RX pins even when operating
at 3.3V (as we have it here).

The duplex function (Dynamixel bus uses one single data wire for transmit _and_ receive) is provided
by two [74LVC2G241](https://www.ti.com/product/SN74LVC2G241?keyMatch=74LVC2G241&tisearch=Search-EN-everything&usecase=GPN)
dual-buffer controlled by the RTSA pin of each UART.

>**Note:**
>In order for the buses to work, you need to configure the serial ports in sofware in RS485 mode after
>the ports are opened.
>The following is an example of how this would be done in Python:
>
```python
from serial import rs485, Serial
port = Serial('/dev/ttySC0', 1000000)
port.rs485_mode = rs485.RS485Settings()
... 
# now you can use the port as needed
...
```

The use of SPI interface has a significant advantage for Dynamixel communication: the
interface has no packet lag like in the case of USB. At minimum this can be reduced
to 1ms per packet (see more details in [this](https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_wizard2/#usb-latency-setting)
section of the instructions from Robotis). This latency does not happen for SPI devices and
this allows much higher synchronization frequencies, expecially for devices that have
not support for [Protocol 2.0](https://emanual.robotis.com/docs/en/dxl/protocol2/)
and methods like BulkRead, SyncRead, BulkWrite.

The board supports a variety of connectors:

- MOLEX SPOX for Dynamixel AX and MX series
- Molex Micro Latch for Dynamixel XL-320 servo
- JST-XE for the new X series servos.

There are 3 connectors for each of the two buses provided by the SC16IS752 chip. This
allows very convenient connectivity for chains of dynamixel servos without needing
to use other connectivity hubs. All 6 devices share the same ground and power rails,
so care must be taken not to mix servos that have different voltage requirements.

## Sensors

### IMU

The board has a [LSM6DS3](https://www.st.com/en/mems-and-sensors/lsm6ds3.html)
chip installed that provides 3 axis accelerometer and 3 axis gyroscope readings.
The device is connected over I2C and is avaialable at address 0x6a.

### ADC

The board includes also a 4 channels ADC interface [TLA2024](https://www.ti.com/product/TLA2024)
used to monitor the voltages:
- AIN0 is connected to the +3.3V rail
- AIN1 is connected to the +5V rail through a divisor 1/2
- AIN2 is connected to the Dynamnixel Bus VCC rail through a divisor 1/4

The chip is connected to the I2C bus and is available at address 0x48.

## FAN control

The HAT includes a small circuit that allows the pin GPIO12 (PWM0) to control
one or two fans (in the same time - there are two JST-PH connectors avaialable).
This allows you to use a fan heatsink installed directly on top of the Raspberry
Pi main SoC and a second fan in the robot case to control effectively the
air flow.

## HAT EEPROM

The HAT has an 32K I2C EEPROM [CAT24C32YI](https://www.onsemi.com/products/memory/eeprom-memory/cat24c32)
used to store the device tree configuration for the HAT, allowing "hands-free" installation
of the drivers and activation of the functions provided by the HAT.

## TFT connector

At the side of the board there is a 11 pin female header that is provided specifically
for the [Adafruit 2.0" IPS TFT](https://www.adafruit.com/product/4311). This is a
no touch 320 x 240 TFT dysplay that you can use to provide monitoring information
from the robot (ex. joint status).

For the time being the installation is performed using the
[excellent script from Adafruit](https://github.com/adafruit/Raspberry-Pi-Installer-Scripts/blob/master/adafruit-pitft.sh)
but we plan to move everything in the EEPROM and use the Device Tree instead.

Since the script shows the console on the display it acts exactly as a (smaller)
display, allowing for very convenient UI design using things like Qt or courses.
