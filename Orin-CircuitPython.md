## <u>AI Lab Notes</u>

#### Extending the Jetson Python environment for programming and data acquisition with CircuitPython and ARM microcontrollers

Previous AI Lab Notes covered using AGX Orin with USB-interfaced AVR (Arduino) and ARM Cortex M [microcontrollers for edge-based modeling, stimulation,](https://github.com/rtrelease/Jetson-Symbolics/blob/main/M4_Controller-CorticalMicrocircuitLayout.md) and process control applications.

Initial work focused on [integrating the legacy Java-based Arduino IDE](https://github.com/rtrelease/Jetson-Symbolics/blob/main/Arduino2.md) with the aarch64 Ubuntu-based Linux for Jetson (L4J) environment.  

The Arduino IDE on AGX Orin communicated directly with microcontrollers via USB using serial TTY protocol via **/dev/ttyACM0**, and C based "sketches" could be debugged, loaded directly into MCU RAM and run. Program process output was directed to the IDE's built-in serial console.

In fact, the ARM Cortex M series MCUs used in our work shipped with CircuitPython installed for board programming, which could be reversibly 'overwritten' for using Arduino IDE C sketches.

However, given the extensive use of well-integrated Python in NVIDIA's Jetson machine learning and computer vision development resource frameworks, it seemed worthwhile to evolve new Python-CircuitPython programs to run on the AGX Orin-connected MCUs.

In *'good old' basic programming methods*, Python and CircuitPython can certainly be coded with a plain text editor and tested with a serial console/terminal connected to the target MCU board.  

So we first followed [Adafruit's basic setup and testing recommendations](https://learn.adafruit.com/welcome-to-circuitpython/advanced-serial-console-on-linux) for its CircuitPython MCUs, then proceeded to installing Python Spyder for a scientific grade IDE compatible with the rest of the Jupyter Notebook (scipy, numpy etc) environment.

Install the native Ubuntu *screen* console application for standard USB-tty communications:

		sudo apt install screen

As an alternative console, install native Ubuntu *tio* application for auto-resetting USB-tty communications:

		sudo apt install tio

Testing *screen* application for communications and CircuitPython access with a USB-connected M4F MCU:

		ls /dev/ttyACM0


![Orin-M4Express-ttyACM0](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/fcfa814c-4edf-4ed5-8ec4-06222ddb95ae)

![Orin-M4Express-ttyACM0-2](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/eb6c09e1-3e39-486a-83ae-b3218458583b)
