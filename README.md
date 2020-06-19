# SPR2005

Robotics HAT for Raspberry Pi

<img src="./imgs/rev1-iso-TFT.jpg?raw=true" width="500px">

This HAT contains the following components:

- 5V / 3A high performance DC-DC switching supply that powers the
  Raspberry Pi from external power source or LiPo batteries
  (from the Dynamixel bus)
  
- Dual Dynamixel bus with very low latency (connected on SPI and
  not USB like the standard Robotis interfaces); supports speeds
  up to 2Mbps and the board can be fitted with Moles SPOX (for
  AX and MX servos), Molex Micro Latch (for XL-320) or JST-XE
  (for X series servos). Only TTL models supported (no RS485)
  
  
- IMU - LSM6DS3 Accelerometer and Gyroscope connected on I2C
 
- 4 channel ADC (TLA2024) connected on I2C for voltage monitoring

- WM8960 stereo codec with dual onnidirectional mics and output
  for 2 x 1W speakers, connected on I2S bus and used for speech
  control / recognition and synthesis
  
- PWM fan control using BCM GPIO12; two connectors are provided
  one for fans on heatsink and one for fan on the robot case
  
- connector for a 2.0" 320 x 240 IPS Diplay from Adafruit using
  ST7789 and connected on SPI (CE1).

## Detail Description

Revision  | Details
----------| -------
rev A.1.  | Initial release. [Details](REV1.md)
rev B.1.  | Change power plug, added CP2104 for console access allowing access direct over USB, changed pin header to a through hole one as the SMD only go to 7.5mm and we wanted higher clearance from Raspberry Pi (and possible compatibility with [Coral](https://coral.ai/products/dev-board/) boards).
