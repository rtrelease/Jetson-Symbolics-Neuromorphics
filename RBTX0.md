## <u>AI Lab Notes</u>

#### ***Jetson RBTX: Intelligent lab microcontrollers and robotic data systems development***

Jetson systems themselves incorporate multiple sub-processors and provisions for using additional external microprocessors, including a CAN bus controller.

Previous Lab Notes have covered the use of Orin-programmed and USB-hosted Arduino-compatible ARM Cortex M series 32 bit microcontrollers for hybrid device process control and neuromorphic simulators. CircuitPython has become their primary programming language.

Newer Bluetooth Low Energy (BLE) compatible multi-sensor MCUs have also been put to work as mobile robot controllers and then as data acquisition sub-systems for physiological recording. 

Left, Adafruit CircuitPlayground Bluefruit (CPB; Nordic nrf52840 M4) on Crickit robotic drivers board; right, standalone SD logger on CPB
![image](https://github.com/user-attachments/assets/1dbb6414-f6ad-43e0-a9c1-d8fb5c1c0a96)

The first set of instruments recorded live pulse and heart rate data for continuing prior behavioral neurophysiology research on heart rate and rhythm control.

Orin recording and plotting of fingertip *plethymography* data from basic CPB LED+sensor
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/d1efbbcb-2319-44e4-9c71-8907daa23c82)

Adafruit Clue (nrf52840) recording from Scoshe BLE wireless heart rate monitor band
![image](https://github.com/user-attachments/assets/3ddc7a50-f78d-4304-9a2f-ce9941ac9323)

Clue with Crickit, CPB with SD logger, and Feather Bluefruit Sense MCU with TFT panel displaying scans of all built-in sensors common to these nrf52840 boards
![image](https://github.com/user-attachments/assets/eec72879-aad0-4d5b-964b-f24d917cf5af)

With light, color, proximity, temperature, humidity, 3-axis accelerometry, gyroscopic, sound,  barometric pressure, altimetry and magnetic sensing, and motor control outputs, these MCU boards fit well into the 'silicon brainstem' metaphor for robotics designs.


Feather Bluefruit Sense with 3.5" TFT Feather Wing interrupting sensor scan program to run CircuitPython REPL, with USB connection to Xeon workstation
![image](https://github.com/user-attachments/assets/118db4b2-3700-4471-b921-f27358ad2074)


Running Dave Astels' CircuitPython *def* function code example using Mu Editor serial connection with Feather Sense
![image](https://github.com/user-attachments/assets/1dc109ba-8c69-4c25-af16-e476cc8c37a7)


