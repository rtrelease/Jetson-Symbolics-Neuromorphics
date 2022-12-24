## <u>AI Lab Notes</u>

#### **Controlling *neuronal* hardware simulators:** Gearing up for Arduino microcontrollers and all **THAT**

One of the objectives of all this development 
is to support ongoing AI research into new network models that operate like analog integrating 'spiking' biological neurons, especially cortical microcircuits. 

About 10 years ago, I was working on another platform with a simulator that used 16 Arduino Micros to model individual spiking, analog threshold-triggered pyramidal cells and interneurons in a simple neocortical microcircuit.  

A relatively simple *apt install* of the Arduino IDE to the existing Java environments for the current Xeon RTX workstation and the ARM64 Jetson AGX Orin Developer Kit gave access to this original legacy simulator programming and its Arduino Mega 2560 microcontroller.

Not only *that*, but it gave access to *hybrid computing* with a new open source analog computer -- [The Analog Thing](https://the-analog-thing.org/wiki/) (THAT) -- capable of realtime modeling of dynamically spiking cortical microcircuit neurons.  

Analog computing master expert Dr. Bernd Ulmann of Anabrid has kindly provided the Arduino Mega 2560 design and code for a [hybrid controller and data logger](https://github.com/anabrid/hardware/tree/main/the-analog-thing/arduino_2650_hybrid_controller) designed for THAT.  

##### A straightforward apt install and the Arduino THAT controller code was up on the AGX Orin DK, ready to go for a new Arduino Mega hybrid controller and brain microcircuit modeling!
![image](https://user-images.githubusercontent.com/71346897/209422743-8bd2314a-04fa-46f0-9b8c-a72afa013f2d.png)
To be continued...
