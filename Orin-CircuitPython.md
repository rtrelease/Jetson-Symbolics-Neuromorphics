## <u>AI Lab Notes</u>

#### Extending the Jetson Python environment for programming and data acquisition with CircuitPython and ARM microcontrollers

Previous Lab Notes have covered using AGX Orin with USB interfaced ARM Cortex M microcontrollers for modeling, stimulation and process control applications.

The initial work focused on integrating the legacy Arduino IDE with the aarch64 Ubuntu-based Linux for Jetson (L4J) environment.


Install native Ubuntu *screen* application for standard USB-tty communications:

		sudo apt install screen

Install native Ubuntu *tio* application for auto-resetting USB-tty communications:

		sudo apt install screen

Testing *screen* application for communications and CircuitPython access with a USB-connected M4F MCU:

		ls /dev/ttyACM0

![Orin-M4Express-ttyACM0](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/fcfa814c-4edf-4ed5-8ec4-06222ddb95ae)

![Orin-M4Express-ttyACM0-2](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/eb6c09e1-3e39-486a-83ae-b3218458583b)

