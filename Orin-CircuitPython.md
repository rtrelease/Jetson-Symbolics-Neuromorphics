## <u>AI Lab Notes</u>

#### Extending the Jetson Python environment for programming and data acquisition with CircuitPython and ARM microcontrollers

Previous Lab Notes covered using AGX Orin with USB-interfaced AVR (Arduino) and ARM Cortex M [microcontrollers for modeling, stimulation,](https://github.com/rtrelease/Jetson-Symbolics/blob/main/M4_Controller-CorticalMicrocircuitLayout.md) and process control applications.

Initial work focused on [integrating the legacy Java-based Arduino IDE](https://github.com/rtrelease/Jetson-Symbolics/blob/main/Arduino2.md) with the aarch64 Ubuntu-based Linux for Jetson (L4J) environment.  

The Arduino IDE on AGX Orin communicated directly with microcontrollers via USB using serial TTY on **/dev/ttyACM0**, and C based "sketches" could be debugged, loaded directly into MCU RAM and run. Program process output was directed to the IDE's built-in serial console.

The ARM Cortex M series MCUs used in our work shipped with CircuitPython installed for board programming, which was reversibly 'overwritten' for using Arduino IDE C sketches.

However, given the extensive use of well-integrated Python in NVIDIA's Jetson machine learning and computer vision development resource frameworks, it seemed worthwhile to evolve new Python-CircuitPython programs to run on the AGX Orin connected MCUs.

https://learn.adafruit.com/welcome-to-circuitpython/advanced-serial-console-on-linux

Install native Ubuntu *screen* application for standard USB-tty communications:

		sudo apt install screen

Install native Ubuntu *tio* application for auto-resetting USB-tty communications:

		sudo apt install tio

Testing *screen* application for communications and CircuitPython access with a USB-connected M4F MCU:

		ls /dev/ttyACM0

![Orin-M4Express-ttyACM0](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/fcfa814c-4edf-4ed5-8ec4-06222ddb95ae)

![Orin-M4Express-ttyACM0-2](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/eb6c09e1-3e39-486a-83ae-b3218458583b)

