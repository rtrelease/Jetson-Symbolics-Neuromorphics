#### Sorting the IDEs of MCUs, from *AVRs* to *ARMs*

Previous Lab Notes highlighted NVIDIA's new commitment to direct ***apt*** installation of Jetson AGX Orin JetPack software in lieu of the prior NV SDK downloads from a workstation.

In that 'spirit', despite years of strong recommendations to install the latest binaries from the Arduino.cc web site, I intitially chose *apt install arduino* to get the IDE up on both Xeon workstation and AGX Xavier dev kit.

The Apt Repository served up Arduino IDE 2.1.0.5 for both systems neatly, and both could immediately detect a USB connected Arduino Mega 2560, compile and run new code through the standard IDE drop down menus.

The image immediately below shows the Orin loading the standard Blink example.

![image](https://user-images.githubusercontent.com/71346897/211944288-7e1ea393-f51b-4129-800b-24fb36ffc5ff.jpeg)

