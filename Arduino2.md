## <u>AI Lab Notes</u>

#### Sorting the IDEs of MCUs, from *AVRs* to *ARMs*

Earlier Lab Notes highlighted NVIDIA's 2022 commitment to direct ***apt*** installation of Jetson AGX Orin JetPack software in lieu of the prior standard installs from a NV SDK workstation.

In that 'spirit', despite years of strong dev recommendations to install the latest binaries directly from the Arduino.cc web site, I initially chose *apt install arduino* to get the IDE up on both Xeon workstation and AGX Xavier dev kit.

The Apt Repository served up Arduino IDE 2.1.0.5 for both Ubuntu-based systems neatly, and both installed apps could immediately detect a USB-connected Arduino Mega 2560, compile, load, and run new code on it through the standard IDE drop down menus.

##### *This shows the Orin loading and running the standard Blink example:*
![image](https://user-images.githubusercontent.com/71346897/211949994-44ac7020-c0b0-4852-8e20-7837a2a7ff54.jpeg)

It sure looked and worked like the *good old Arduino* 1.n IDE I had used for nearly 20 years on Macs for a variety of projects, even though it was a newer 2.0 progran completely rewritten in *Blahblah*...

*Everything looked fine for programming the planned analog computer hybrid interface*, **until I tried to add new hardware definitions via URL as for earlier projects.**

*There was no standard Board Manager selection on the 2.1.05 IDE Tools Menu, and there was no Preference Window element for entering URLs for additional boards and processors.*

Hence, no way to load the other boards libaries I had used for my prior work with non-Arduino AVR and ARM Cortex M boards...

So the next step was sudo apt remove arduino on two systems, and follow Arduino.cc's best instructions for installing IDE packages for Linux systems.


![image](https://user-images.githubusercontent.com/71346897/211956552-4c7c4c3b-9cd2-4a77-b062-a73a1468c0d6.png)



![image](https://user-images.githubusercontent.com/71346897/211956806-2b375334-26c8-40af-86b1-85cbf9144777.jpeg)
