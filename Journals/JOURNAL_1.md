# Journal Entry 1

### Week 1 : September 14th ~ 20th, 2026

This was the first week of creating ORION; a thrust-vector controlled model rocket with custom firmware, avionics, and hardware. My goal with ORION is to create a model rocket that is actively stabilized using PID control and demonstrates almost perfect vertical ascent (similar to BPS.Space's SCOUT rocket). 

Throughout the week, I adjusted and rethinked our requirements and constraints for this rocket. The finalized requirements I came up with are:

1) Must be controlled via TVC (2 Servos controlling motor through a gimbal attachment)
2) Uses F-15 Rocket Motor
3) Reach an altitude range between 30 meters ~ 40 meters
4) Custom Flight Controller

Now let's get into what I actually worked on this week:

## Schematic Design

### Power System (70% Complete):
- Orion will be powered by a 2S LiPo battery.
- The power system has 5 power rails:
   - 5V — A TPS62933 buck converter is used to convert the nominal voltage ~7.4 of the battery into 5V output used for the MCU and other future needs
   - 5V_SERVO — The servos receive a different power rail than the 5V supplied to the MCU in order to avoid transient voltage spikes and current draws from the servos during sudden changes, and the high-switching from the servos which would disrupt signals from the MCU. A TPS563203 buck converter is used to produce low-noise and other components are used to avoid the voltage/current backlash
   - 3V3_ANALOG — Isolated power rail to avoid high-frequency switching noise from MCU and other digital components. A LDO (Linear Dropout Regulator) is used to step down 5V into 3V3 low-noise voltage required by sensors and other ICs.
   - 3V3_DIGITAL — Same as 3V3_ANALOG but more noisy and greater frequency switching. The LDO used is: TLV75733PDRV
   - 1V8 — Stepping 3V3_ANALOG down to 1V8 for IMU sensor ICM-20948
 
<img width="2650" height="1825" alt="Screenshot 2026-09-20 at 11 50 06 PM" src="https://github.com/user-attachments/assets/aab7f340-4209-4533-8922-07ad9029f95f" />
 
### Navigation Sensors (98% Complete):
- Orion will host 3 IMUs for redundancy and sensor fusion, 2 Barometers, 1 Magnetometers. The design choice for choosing 3 IMU was made to improve reliability by allowing Orion to compare sensors and detect faulty or inconsistent measurements. The same goes for choosing two different barometers, we can compare altitude/pressure measurements from both sensors to clear up any inconsistences. 
- There is also a level shifter so that the MCU can communicate with the IMU sensor ICM-20948, since the IMU outputs in 1V8 while the MCU only accepts 3V3.

<img width="2644" height="1817" alt="Screenshot 2026-09-20 at 11 50 25 PM" src="https://github.com/user-attachments/assets/24680e23-f889-4c6f-bad6-29a58ffc1960" />

### Flight Compute Module (30% Complete):
- Orion will be using the STM32F7 microchip as it has 6 SPI, 4 I2C, additional communication protocols (UART, SDMMC, etc). The clocking speed is very good, around 480 MHz, and it has a lot of compute power.
- Added micro-SD Card to store and log flight data after the flight.
- Added Flash Chip to store and record flight data during the actual flight, it will transfer all data to the SD card after landing/reaching the ground.
- Added USB-B connection for programming, and debugging.
- Added the necessary components needed to operate the MCU
  - Decoupling Capacitors
  - External Crystal Osciallator
  - VREF+ Filter (VREF+ sets the ADC's voltage measurement range, while the VREF filter keeps that reference voltage clean and stable by reducing electrical noise.)
  - Added all sensors to the MCU via SPI protocol

 <img width="2723" height="1899" alt="Screenshot 2026-09-21 at 12 04 45 AM" src="https://github.com/user-attachments/assets/8197a994-251d-401a-80b5-1fbc5ff4ccb4" />

### FCC Peripherals (1% Complete):
- Just started, this sub-schematic will have connections to the servos, pyro connections, and other external peripherals

## CAD
Started the overall shape and look of the rocket. It will be supported by 4, 5mm tubes and the nose cone shape is elliptical. The height of the rocket will be 0.9 meters, and the diameter of the rocket will be 0.075 meters or 7.5 cm. 

CAD Image:

<img width="2219" height="1865" alt="Screenshot 2026-09-21 at 12 09 04 AM" src="https://github.com/user-attachments/assets/418e822f-a9f6-4de6-9a28-1b3ddecae2d9" />

NEXT STEPS:
- Finish entire schematic and start PCB layout and maybe even routing by end of next week
- 3D print the CAD files and conduct tests on the rocket to find center of mass, total mass, and much more
- Use the calculations/observations from the tests to simulate the rocket in OpenRocket (failed this week)
- Test the TVC/gimbal
