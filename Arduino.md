## <u>AI Lab Notes</u>

#### **Controlling *neuronal* modeling hardware:** Gearing up for Arduino microcontrollers and all **THAT** analog modeling

One of the objectives of all this development is also to support ongoing AI research into new neuromorphic or biomimetic neural network models that operate *unclocked in realtime*, like analog integrating 'spiking' biological neurons, especially [cortical microcircuits](https://academic.oup.com/book/24640). 

A decade ago, I was working on another platform with a simulator that used 16 Arduino Micros to model networked, spiking, analog threshold-triggered pyramidal cells and interneurons in a simple neocortical microcircuit.  

Today, a simple trusted *apt repository install* of the Arduino IDE in the Java environments of both the current Xeon RTX workstation and the ARM64 Jetson AGX Orin DK gave access to the original legacy simulator programming and its Arduino Mega 2560 microcontroller.

Not only *that*, but it prepared the development systems for *hybrid computing* with a new open source ***analog computer*** -- [The Analog Thing](https://the-analog-thing.org/wiki/) (THAT) -- capable of [realtime modeling of dynamically spiking](https://the-analog-thing.org/docs/dirhtml/rst/applications/hindmash_rose_neuron/spiking_neuron/) cortical microcircuit neurons.  

Analog computing master expert Dr. Bernd Ulmann of Anabrid has kindly provided the Arduino Mega 2560 design and code for a simple [hybrid computing controller and data logger](https://github.com/anabrid/hardware/tree/main/the-analog-thing/arduino_2650_hybrid_controller) designed for THAT.  Since the Arduino IDE  communicates with THAT's microcontroller via USB, the ARM Jetsons avoid obstacles of only x86-specific data acquisition system drivers being available for use with these neuronal simulators.

When the Arduino hardware is finally integrated with THAT systems, it will enable new hybrid computing synergies between digital AI, deep learning methods and analog computer models' dynamical data.


###### A straightforward  *apt install Arduino*, and downloaded THAT controller code was loaded on the IDE on the AGX Orin DK; ready to go for a new Mega 2560 hybrid controller, THAT, and brain microcircuit modeling!
![image](https://user-images.githubusercontent.com/71346897/209422743-8bd2314a-04fa-46f0-9b8c-a72afa013f2d.png)
To be continued...
