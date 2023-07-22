**Extending the Jetson Python environment for programming and data acquisition with CircuitPython and ARM microcontrollers**

Previous Lab Notes have covered the use of AGX Orin with USB interfaced microcontrollers for modeling, stimulation and process control applications.

The initial work focused on integrating the legacy Arduino IDE with the aarch64 Ubuntu-based Linux for Jetson (L4J) environment.


Install native Ubuntu *screen* application for USB-tty communications:

sudo apt install screen

Install native Ubuntu *tio* application for auto-resetting USB-tty communications:

sudo apt install screen

Testing *screen* application for communications and CircuitPython access with a USB-connected M4F MCU:

ls /dev/ttyACM0

