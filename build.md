# Build Instructions

This is the build instructions for the Tornblue keyboard.

## PCB Assembly

You can order the PCBs with PCBA, in this case the PCBs come mostly assembled. A few components need to be hand soldered: the Holyiot module, JST-PH connector and underglow LEDs.

### 5 or 6 columns

If you are building a 5 column Tornblue keyboard you need to fist break off the outer column.

### Holyiot module

The Holyiot module is designed to be mounted on another PCB. You can see a guide on [how to solder castellated mounting holes](https://learn.sparkfun.com/tutorials/how-to-solder-castellated-mounting-holes/all).

You'll need a fine tip soldering iron, as the castellations are a narrow pitch. Be careful to not apply too much solder, as it is easy to short between the pins. Don't short between the pins and the metal shield on the module. It can be good to have a solder sucker and desoldering wick to clean up the joints if required.

### JST-PH connector

The JST-PH connector is soldered on the underneath of the PCB.  The two pads towards the top of the board connect the battery, and the lower two pads are for mechanical support.

You can use the same technique as the Holyiot module; first apply some solder to one of the pads. Reheat the solder and slide the JST-PH connector in place. Check the connector is correctly positioned, and adjust if required. Then solder the remaining three pads.

### Underglow LEDs (optional)

The WS2812B 5050 leds need to be soldered in the correct orientation, on the underside of the board. There is a mark on one corner of the leds, this should be pointing to the top left of the PCB - next to the small vertical mark on the silkscreen. Carefully solder all six WS2812B leds.

You should install the bootloader, zmk and test the PCB before adding the key switches, battery and case.

## Bootloader and zmk

You need to program the Holyiot module with a bootloader and the zmk keyboard firmware.

### Bootloader 

The PCB is designed for a SOIC-8 test clip to be used to flash the bootloader. You need to modify your test clip using these [instructions](https://github.com/SimonMerrett/SOICbite/blob/master/HOWTO_mod_clip.md). Or you could solder wires to the SWD pads for the programming.

You can use the Adafruit nRF52 bootloader. Download the pre-built [pca10056 version](https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases).

#### ST Link v2

You need to connect the SWD pads on the PCB to the 20 pin JTAG header on the ST Link v2 as follows:

|PCB SWD|ST Link v2  |
|--|--|
|SWD IO|Pin 7|
|SWD CLK|Pin 9|
|GND|Pin 8|

Connect both the ST Link v2 USB and keyboard USB to your PC. The blue charging LED on the keyboard will blink, this is normal when no battery is attached.

Install openocd, and run the following command:

    openocd -f interface/stlink.cfg -f target/nrf52.cfg -c "gdb_flash_program enable" -c "gdb_breakpoint_override hard" -c "init" -c "reset halt" -c "flash write_image erase ./pca10056_bootloader-0.6.2_s140_6.1.1.hex"

If this is successful it will program the bootloader, the console output should look like:

	Open On-Chip Debugger 0.11.0
	Licensed under GNU GPL v2
	For bug reports, read
	 http://openocd.org/doc/doxygen/bugs.html
	Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'. 
	Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
	
	nRF52 device has a CTRL-AP dedicated to recover the device from AP lock.
	A high level adapter (like a ST-Link) you are currently using cannot access
	the CTRL-AP so 'nrf52_recover' command will not work. 
	Do not enable UICR APPROTECT.

	force hard breakpoints

	Info : clock speed 1000 kHz
	Info : STLINK V2J31S7 (API v2) VID:PID 0483:3748
	Info : Target voltage: 3.261214
	Info : nrf52.cpu: hardware has 6 breakpoints, 4 watchpoints
	Info : starting gdb server for nrf52.cpu on 3333
	Info : Listening on port 3333 for gdb connections 
	target halted due to debug-request, current mode: Thread 
	xPSR: 0x01000000 pc: 0x00000a80 msp: 0x20000400 
	Info : nRF52840-xxAA(build code: D0) 1024kB Flash, 256kB RAM 
	Info : Padding image section 0 at 0x00000b00 with 1280 bytes Info : Flash write discontinued at 0x00025de8, next section at 0x000f4000 
	Warn : Adding extra erase range, 0x00025de8 .. 0x00025fff Info : Padding image section 2 at 0x000fc5b4 with 4684 bytes 
	Warn : Adding extra erase range, 0x000fd858 .. 0x000fdfff 
	Warn : Adding extra erase range, 0x10001000 .. 0x10001013 
	Warn : Adding extra erase range, 0x1000101c .. 0x10001fff 
	auto erase enabled 
	wrote 194120 bytes from file ./pca10056_bootloader-0.6.2_s140_6.1.1.hex in 7.985752s (23.739 KiB/s)

### zmk

You can now install zmk.

#### Building with github actions

(todo)

#### Building on your PC

To build the firmware on your PC follow the [zmk installation instructions](https://zmk.dev/docs/development/setup).

You can build zmk for the left side of the keyboard using:

	west build -p -d build/tornblue_left -b tornblue_left -- -DZMK_CONFIG=/Users/richardt/keyboard/miryoku-zmk-config/config/

You can build zmk for the right side of the keyboard using:

	west build -p -d build/tornblue_right -b tornblue_right

### Install zmk

Connect the left side of the keyboard with USB. Press the reset button quickly twice. You should see a USB drive called `NRF52BOOT`. Copy the `zmk.uf2` from the `tornblue_left` build folder. zmk will be installed and the keyboard will restart.

Repeat this with the right side of the keyboard, but copt the `zmk.uf2` file from the `tornblue_right` build folder.

### Test

You can now test the PCB. Connect the keyboard to your PCB with a USB cable. Use tweezers or similar to short the key switch pads and test that all the keys work, on both sides of the keyboard.

## Key switches and battery

### Encoder (optional)

You can optionally use a horizontal encoder. Position the encoder clip at an angle on the PCB, and carefully rotate it down. Solder the pins.

### Key switches

You can solder either Cheery MX compatible key or Kailh Choc key switches. Remember to insert the key switches in the plate before soldering if required.

### Battery

Plug your battery into to the JST PH connector on the underside of the PCB. **The red wire MUST connect to the positive terminal marked 'PWR (+)'**.

## 3d Printed Case

