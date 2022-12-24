## <u>AI Lab Notes</u>

#### **Controlling *neuronal* hardware simulators:** Gearing up for Arduino microcontrollers and all **THAT** modeling

One of the objectives of all this development is also to support ongoing AI research into new neuromorphic or biomimetic network models that operate *unclocked in realtime*, like analog integrating 'spiking' biological neurons, especially [cortical microcircuits](https://academic.oup.com/book/24640). 

A decade ago, I was working on another platform with a simulator that used 16 Arduino Micros to model networked, spiking, analog threshold-triggered pyramidal cells and interneurons in a simple neocortical microcircuit.  

Today, a simple *apt repository install* of the Arduino IDE in the existing Java environments for both the current Xeon RTX workstation and the ARM64 Jetson AGX Orin Developer Kit gave access to this original legacy simulator programming and its Arduino Mega 2560 microcontroller.

Not only *that*, but it prepared for *hybrid computing* with a new open source analog computer -- [The Analog Thing](https://the-analog-thing.org/wiki/) (THAT) -- capable of realtime modeling of dynamically spiking cortical microcircuit neurons.  

Analog computing master expert Dr. Bernd Ulmann of Anabrid has kindly provided the Arduino Mega 2560 design and code for a [hybrid controller and data logger](https://github.com/anabrid/hardware/tree/main/the-analog-thing/arduino_2650_hybrid_controller) designed for THAT.  With the Arduino IDE  communicating with the THAT microcontroller via USB, the ARM Jetsons sidesteps issues of x86-specific data acquisition system drivers for data acquisition systems.


###### A straightforward apt install and the Arduino IDE and downloaded THAT controller code was up on the AGX Orin DK, ready to go for a new Mega 2560 hybrid controller, THAT, and brain microcircuit modeling!
![image](https://user-images.githubusercontent.com/71346897/209422743-8bd2314a-04fa-46f0-9b8c-a72afa013f2d.png)
To be continued...
