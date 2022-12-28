## <u>AI Lab Notes</u>

#### **Controlling *neuronal* modeling hardware:** Gearing up for Arduino microcontrollers and all **THAT** analog modeling

One of the objectives of all this development is also to support ongoing AI Lab research into new neuromorphic or biomimetic neural network models that operate *unclocked in realtime*, like analog integrating 'spiking' biological neurons, especially in [cortical microcircuits](https://academic.oup.com/book/24640). 

A decade ago, I was working on MacOS with an experimental simulator that used 16 Arduino Micros to model dynamics of synaptically networked, spiking, analog threshold-triggered pyramidal cells and interneurons in a simple neocortical microcircuit.  

Today, new access to my original legacy simulator programming and its Arduino Mega 2560 microcontroller were provided by now trusted ***apt repository installations*** of the **Arduino IDE** in the *enhanced* Ubuntu OpenJDK Java environments of both the Xeon RTX GPU workstation and the ARM64 Jetson AGX Orin DK.

Not only *that*, but this prepared the Jetson development systems for *hybrid computing* with a new Open Source ***analog computer*** from **Anabrid** -- [The Analog Thing](https://the-analog-thing.org/wiki/) (THAT) -- capable of [realtime dynamical modeling of action potential bursting](https://the-analog-thing.org/docs/dirhtml/rst/applications/hindmash_rose_neuron/spiking_neuron/) of cortical microcircuit neurons.

THAT has a built-in hybrid computing interface with 16 pin DIP cable header, along with connectors for parallel-chaining multiple THATs for a larger analog computing array.

Analog computing master expert Dr. Bernd Ulmann of Anabrid has kindly provided the Arduino Mega 2560 code for a simple [hybrid computing controller and data logger](https://github.com/anabrid/hardware/tree/main/the-analog-thing/arduino_2650_hybrid_controller) he designed for THAT.  Since the Arduino IDE serial communicates with THAT's microcontroller via USB, the ARM Jetsons can sidestep obstacles of only x86-specific data acquisition (DAQ) systems/drivers being available.

When this initial Arduino Mega 2560 microcontroller hardware is finally integrated with THAT systems, it will enable new hybrid computing synergies between digital AI, deep learning methods and analog computer models' dynamical data outputs.

A continuing review of Dr. Ulmann's Analog Paradigms advanced 16 channel [Hybrid Controller module documentation](https://analogparadigm.com/downloads/hc_handbook.pdf) indicates that there may be a reasonable upgrade path for an Open Source THAT hardware interface that can include a 32 bit ARM Cortex M controller system with greater analog and digital I/O capabilities and processing speed than the 8 bit Atmel atMega 256.

This might be a 'Mega-footprint' MCU, like the Arduino Due controller used in my prior Teensy 3.1 ARM M4F hardware redesign of the original Arduino Micro neocortical microcircuit simulator.

Of course, beyond serving as hybrid controller for an analog computer and neuronal circuit behavior simulations, these well-supported maker-popular Arduino IDE compatible MCU systems can provide up to 12 channels of A/D, 52 digital I/O channels and 2 channels of base D/A suitable for many other laboratory data acquisition and control applications, such as robotics and 3D printer control.

**To be continued...**


###### A straightforward  *apt install Arduino*, and downloaded THAT controller code was loaded on the IDE on the AGX Orin DK; ready to go for a new Mega 2560 hybrid controller, THAT, and brain microcircuit modeling!
![image](https://user-images.githubusercontent.com/71346897/209422743-8bd2314a-04fa-46f0-9b8c-a72afa013f2d.png)

RBT 26 December 2022
