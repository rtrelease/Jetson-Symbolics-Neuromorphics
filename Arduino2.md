#### Sorting the IDEs of MCUs, from *AVRs* to *ARMs*

Earlier Lab Notes highlighted NVIDIA's 2022 commitment to direct ***apt*** installation of Jetson AGX Orin JetPack software in lieu of the prior standard installs from a NV SDK workstation.

In that 'spirit', despite years of strong dev recommendations to install the latest binaries directly from the Arduino.cc web site, I initially chose *apt install arduino* to get the IDE up on both Xeon workstation and AGX Xavier dev kit.

The Apt Repository served up Arduino IDE 2.1.0.5 for both systems neatly, and both installed apps could immediately detect a USB-connected Arduino Mega 2560, compile, load, and run new code through the standard IDE drop down menus.

The image immediately below shows the Orin loading the standard Blink example.

![image](https://user-images.githubusercontent.com/71346897/211944288-7e1ea393-f51b-4129-800b-24fb36ffc5ff.jpeg)

It sure looked and worked like the *good old Arduino* IDE I had used for nearly 20 years an Macs for a variety of projects.  

*Everything looked fine for programming the planned analog computer hybrid interface*, **until I tried to add new hardware definitions via URL as for earlier projects.**

