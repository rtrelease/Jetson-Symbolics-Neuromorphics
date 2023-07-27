## <u>AI Lab Notes</u>

### Extending the Jetson Python environment for programming and data acquisition with CircuitPython and ARM microcontrollers

Previous AI Lab Notes covered using AGX Orin with USB-interfaced AVR (Arduino) and ARM Cortex M [microcontroller boards for edge-based modeling, neuromorphic simulation,](https://github.com/rtrelease/Jetson-Symbolics/blob/main/M4_Controller-CorticalMicrocircuitLayout.md) and process control applications.

Initial efforts focused on [integrating the legacy Java-based Arduino IDE](https://github.com/rtrelease/Jetson-Symbolics/blob/main/Arduino2.md) with the aarch64 Ubuntu-based Linux for Jetson (L4J) operating system environment.  

The Arduino IDE on AGX Orin communicated directly with microcontrollers via USB using serial TTY protocol on **/dev/ttyACM0**, and C based program "sketches" could be debugged, compiled, loaded directly into MCU RAM and run. Program process output was directed to the IDE's built-in serial console. IDE processes were simple, convenient, and reliable!

In fact, the ARM Cortex M series MCUs used in our work shipped with CircuitPython installed for board programming, which could be reversibly 'overwritten' for using Arduino IDE C sketches.

However, given the extensive use of well-integrated Python in NVIDIA's Jetson machine learning and computer vision development resource frameworks, it now seemed very worthwhile to evolve new Python - CircuitPython (CP) programs to run on the AGX Orin-connected MCUs.

For *'good old' basic programming methods*, Python and CircuitPython can certainly be coded with a plain text editor and tested with a serial console/terminal connected to the target MCU board.  

So on the AGX Orin Developer Kit, we first followed [Adafruit's basic setup and testing recommendations](https://learn.adafruit.com/welcome-to-circuitpython/advanced-serial-console-on-linux) for Linux on its CircuitPython ARM Cortex M-series MCU boards.  *Then we proceeded to install Python Conda Miniforge3 for an aarch64 conda environment manager.  

Although we also installed Spyder for scientific grade IDE compatible with the rest of the Jupyter scientific computing environment (scipy, numpy, mathplotlib, etc), it proved too specialized for convenient use with CircuitPython. 

After studying Adafruit's IDE recommendations for Linux-based host systems, [thonny](https://thonny.org/) turned out to the best one to install on ARM64 Jetsons with its simplified learning-oriented design and easy MCU/CP.

### From the top: Basic serial console for interactive CircuitPython MCU coding

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

Beyond interactively programming with standard Python, CircuitPython gives direct access to the range of analog and digital I/O pins on the specific MCU board, including available ADC, DAC, SPI, I2C, MOSI/MISO/SCK, and timers.

Externally edited main.py programs are written to the MCU's RAM, which appears as a CIRCUITPY volume on the Jetson Linux filesystem and files manager.

### Installing Miniforge with Conda and Jupyter; Spyder IDE

On x86-64 workstations hosting the NVIDIA SDK, the *Anaconda* Python environment manager is easily installed, providing the Navigator GUI for a standard suite of applications including Jupyter Notebook and Spyder.  However, ARM64 systems like Jetsons must use more lightweight *conda* Python enviroments like Miniforge, mamba, and Mambaforge to support dependencies and applications including Jupyter Notebook and the Spyder.

Sahil Ramani has provided a very useful [guide](https://www.sahilramani.com/2021/11/how-to-setup-python3-and-jupyter-notebook-on-jetson-nano-faster/) for installing Miniforge3, conda, and Jupyter in a Jetson Nano aarch64 Python environment.  The main basic setup process works quite well on AGX Orin as well as Xavier NX, and I'll synopsize it below:

- wget miniforge from its github archive:
  
...
[Installing Spyder with conda](https://docs.spyder-ide.org/current/installation.html#install-conda)
