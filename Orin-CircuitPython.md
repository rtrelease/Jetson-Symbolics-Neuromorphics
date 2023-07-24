## <u>AI Lab Notes</u>

#### Extending the Jetson Python environment for programming and data acquisition with CircuitPython and ARM microcontrollers

Previous AI Lab Notes covered using AGX Orin with USB-interfaced AVR (Arduino) and ARM Cortex M [microcontroller boards for edge-based modeling, neuromorphic simulation,](https://github.com/rtrelease/Jetson-Symbolics/blob/main/M4_Controller-CorticalMicrocircuitLayout.md) and process control applications.

Initial efforts focused on [integrating the legacy Java-based Arduino IDE](https://github.com/rtrelease/Jetson-Symbolics/blob/main/Arduino2.md) with the aarch64 Ubuntu-based Linux for Jetson (L4J) operating system environment.  

The Arduino IDE on AGX Orin communicated directly with microcontrollers via USB using serial TTY protocol on **/dev/ttyACM0**, and C based program "sketches" could be debugged, loaded directly into MCU RAM and run. Program process output was directed to the IDE's built-in serial console.

In fact, the ARM Cortex M series MCUs used in our work shipped with CircuitPython installed for board programming, which could be reversibly 'overwritten' for using Arduino IDE C sketches.

However, given the extensive use of well-integrated Python in NVIDIA's Jetson machine learning and computer vision development resource frameworks, it now seemed worthwhile to evolve new Python - CircuitPython programs to run on the AGX Orin-connected MCUs.

For *'good old' basic programming methods*, Python and CircuitPython can certainly be coded with a plain text editor and tested with a serial console/terminal connected to the target MCU board.  

So on the AGX Orin Developer Kit, we first followed [Adafruit's basic setup and testing recommendations](https://learn.adafruit.com/welcome-to-circuitpython/advanced-serial-console-on-linux) for Linux on its CircuitPython ARM Cortex M-series MCU boards.  *Then we proceeded to install Python Miniforge and Spyder for an aarch64 conda environment and scientific grade IDE compatible with the rest of the Jupyter Notebook (scipy, numpy etc) environment.*

From the top:

- Install the native Ubuntu *screen* serial console application for standard USB-tty communications:

		sudo apt install screen

- As an alternative console, install the native Ubuntu *tio* application for auto-resetting USB-tty communications:

		sudo apt install tio

- Test the *screen* console for communications and CircuitPython access with a USB-**connected** MCU board configured with CircuitPython:

		ls /dev/ttyACM0
  This should return */dev/ttyACM0* if the MCU is active or an error message if there is no connection.

  
- Start the *screen* console with the MCU's serial line name and baud rate:

		screen /dev/ttyACM0 115200
  On startup, the console screen is blank.  Pressing *Return* will halt any resident CircuitPython program running at MCU powerup, then display the microcontroller's version information and the familiar CircuitPython REPL interpreter prompt **>>>** .  

Standard Python code can then be interactively executed on the MCU's CircuitPython interpreter, as seen below:
![Orin-M4Express-ttyACM0-2](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/eb6c09e1-3e39-486a-83ae-b3218458583b)

Beyond interactively programming with standard Python code, CircuitPython gives direct access to the range of analog and digital I/O pins on the specific MCU board, including available ADC, DAC, SPI, I2C, MOSI/MISO/SCK, and timers.

Externally edited main.py programs are written to the MCU's RAM, which appears as a CIRCUITPY volume on the filesystem and files manager.

##### Intalling Miniforge with Conda and Spyder IDE

Sahil Ramani has provided a very useful [guide](https://www.sahilramani.com/2021/11/how-to-setup-python3-and-jupyter-notebook-on-jetson-nano-faster/) for installing Miniforge for an aarch64 *conda* python environment, one that works quite well on AGX Orin
...
