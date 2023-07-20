## <u>AI Lab Notes</u>

#### **ARM Cortex M0+ analog spiking neocortical microcircuit modeling with M4 hybrid controller**, initial layout

- ***Microcircuit simulator can run autonomously on (solar and) rechargeable battery packs***, as well as via the direct USB connection to AGX Orin or RTX workstation that supports Arduino IDE-based MCU artificial neuron programming and data logging
- Each MCU represents a single neuron that communicates with others via analog spike trains and multiple synaptic inputs

##### In media res...

**Canonical neocortical microcircuit** connectivity diagram from [Douglas and Martin (2017)](https://academic.oup.com/book/24640/chapter/187974834)
![image](https://user-images.githubusercontent.com/71346897/213343140-41049d4a-09e4-4563-a68f-a6e6db5b944f.png)


##### =---> Basic AGX Orin-interfaced, 4 M0+ MCUs neocortical microcircuit Series 1 prototype with M4 Metro Grand Central hybrid simulation microcontroller: Successful burst generator code test (thalamic input)
![DSCN1733e](https://user-images.githubusercontent.com/71346897/216524293-94225fb1-044e-4652-b653-15d748d52b5f.jpg)


##### =---> Designing an M4 GC *analog (A0) action potential* (spike) on AGX Orin with Arduino IDE: Scoping the wave
![Screenshot from 2023-02-13 20-12-33](https://user-images.githubusercontent.com/71346897/218638127-e3ac2aa9-6aaa-4fed-b939-558f0ebaeef3.png)

##### =---> Designing MO+ *analog (A0) action potentials* on AGX Orin with Arduino IDE: Wave scaling
![image](https://user-images.githubusercontent.com/71346897/219840406-94709ca3-eabd-46e3-b1c0-5e4feafe03de.png)

##### =---> MO+ cortical microcircuit prototype analog 'neuron', Series 2: An instance of a [Cellular Neural Networks](https://link.springer.com/book/10.1007/978-94-017-0261-4) processor?
![image](https://github.com/rtrelease/Jetson-Symbolics/assets/71346897/1de5ac09-6661-4615-a3e2-b14bae1ed1b4)


##### =---> MO+ cortical microcircuits prototype analog 'neurons', compact Series 3: Before synaptic patching
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/165d44a6-abe2-4330-aadd-e40b0c3333d8)



##### Gratitude to *Lady Ada*, whose excellent Adafruit hardware designs and dedicated production team have made this all possible!
