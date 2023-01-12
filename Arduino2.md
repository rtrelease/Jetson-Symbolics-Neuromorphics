## <u>AI Lab Notes</u>

#### Sorting the IDEs of MCUs, from *AVRs* to *ARMs*

Earlier Lab [Notes](https://github.com/rtrelease/Jetson-Symbolics/blob/main/OrinInstall2Graal.md) highlighted NVIDIA's 2022 commitment to direct ***apt*** installation of Jetson AGX Orin JetPack software in lieu of the prior standard installs from a NV SDK workstation.

So in that 'spirit', despite years of strong dev recommendations to install the latest binaries directly from the Arduino.cc web site, I initially chose to test *apt install arduino*for getting the IDE up on both Xeon workstation and AGX Orin Dev Kit.

The Apt Repository served up Arduino IDE ***"2.1.0.5"*** for both Ubuntu-based systems neatly, and both installed apps could immediately detect a USB-connected Arduino Mega 2560, compile, load, and run new code on it through the standard IDE drop down menus.

##### *This shows the* apt-installed *Orin loading and running the standard Arduino Blink example:*
![image](https://user-images.githubusercontent.com/71346897/211949994-44ac7020-c0b0-4852-8e20-7837a2a7ff54.jpeg)

It sure looked and worked like the *good old Arduino* Java/C++ 1.n IDE I had used for nearly 20 years on Macs for a variety of projects, even though it was a newer 2.0 program (supposedly) completely rewritten in TypeScript/Javascript...

*Everything looked fine for programming the proposed advanced [analog computer hybrid interface](https://github.com/rtrelease/Jetson-Symbolics/blob/main/Arduino.md)*, **until I tried to add new MCU hardware libraries via URL as required for earlier non-Arduino board projects.**

*There was no standard Board Manager selection on the 2.1.0.5 IDE Tools Menu, and there was no Preference Window element for entering URLs for additional boards and processors.*

Hence, no way to load the other non-Arduino libraries I had used for my prior work with non-Arduino AVR and ARM Cortex M boards.

So lesson learned, the next step was ***sudo apt remove arduino*** on two systems, then follow [Arduino.cc's](https://www.arduino.cc/en/software) and [Ubuntu's](https://ubuntu.com/tutorials/install-the-arduino-ide) best instructions for installing IDE packages for amd64 and aarch64 Linux systems.

Consistent with Lab goals, I chose to start with the available latest aarch64 .tar of 1.8.19 for the Orin, with 1.8.19 and the 2.0.3 .tars for x86-64/amd64 for the workstation.  Among other things, this gave me code and performance comparisons of the IDE on three platforms.

**Note that the latest stabile version of Arduino IDE was 2.0.3 at Arduino.cc, compared to 2.1.0.5 claimed by the original apt install...**

Anyway, the version 1.8.19 installs gave the proper Board Manager and Preferences access to all the non-Arduino AVR and ARM Cortex M MCU boards I had been using over the last decade.

The next images show the standard Preferences URL setup and the Board Manager on the Orin confirming connection to an Adafruit Grand Central M4, on a IDE first configured for the Arduino Mega 2560 board.  Function was identical on the Xeon workstation.
![image](https://user-images.githubusercontent.com/71346897/211956552-4c7c4c3b-9cd2-4a77-b062-a73a1468c0d6.png)

![image](https://user-images.githubusercontent.com/71346897/211956806-2b375334-26c8-40af-86b1-85cbf9144777.jpeg)


#### To be continued...
