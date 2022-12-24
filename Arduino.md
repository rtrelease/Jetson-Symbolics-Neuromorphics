## <u>AI Lab Notes</u>

#### **Controlling *neuronal* hardware simulators:** Gearing up for Arduino microcontrollers and all **THAT**

One of the objectives of all this development is to support ongoing AI research into new network models that operate like known analog 'spiking' biological neurons, especially cortical microcircuits. 

About 10 years ago, I was working with a simulator that used 16 Arduino Micros to model spiking, analog threshold-triggered pyramidal cells and interneurons in a simple neocortical microcircuit.  

A relatively simple *apt install* of the Arduino IDE to the existing Java environments for for Xeon RTX workstation and the ARM64 AGX Orin Developer Kit gave access to this original legacy simulator and its Arduino Mega 2560 microcontroller.

Not only *that*, but it gave access to *hybrid computing* with a new open source analog computer -- The Analog Thing (THAT) -- capable of realtime modeling of dynamically spiking cortical microcircuit neurons.  Dr. Bernd Ulmann of Anabrid has kindly provided the Arduino Mega 2560 design and code for a [hybrid controller and data logger](https://github.com/anabrid/hardware/tree/main/the-analog-thing/arduino_2650_hybrid_controller) for THAT.
