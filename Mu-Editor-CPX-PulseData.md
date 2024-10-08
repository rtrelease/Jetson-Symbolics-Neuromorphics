## <u>AI Lab Notes</u>

### Mu Editor installation update and finger plethysmograph pulse rate data acquisition with an ARM M0+ Circuit Playground Express

As previously noted, Adafruit has recommended using the open source Python Mu Editor with its CircuitPython enabled MCU boards, like the Grand Central M4 Express, Metro M4 Express, and Circuit Playground Express (M0+).  

The various Mu installation instructions emphasized Windows, Mac, and standard Linux (amd64) host platforms, but no specifics about ARM64/aarch64 platforms.

Online Mu documentation listed several different Linux alternatives, including GitHub source Python3 compilation, pip3 dedicated environment and apt installs.

As with prior projects, I tried a vanilla Ubuntu 20.04 apt install on my Xeon RTX workstation, but it proved to be very unstabile.  The other alternatives were considered, but none proved satisfactory.

In further investigating the [Mu-Editor GitHub site](https://github.com/mu-editor/mu/releases), it was finally noted that a Debian standard *Mu Editor x86-64 appimage* was made available in response to user requests.  This turned out to provide a very stabile Mu Editor installation on the workstation.

[Mu Editor developer documentation](https://mu.readthedocs.io/_/downloads/en/latest/pdf/)

After all this, it seemed possible that an *ARM64 Ubuntu Mu-Editor apt archive* might have been set up, so I tried a simple 

		sudo apt install mu-editor
  
on the AGX Orin.  It simply worked and was stabile!
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/cb2d70b5-bcb0-4b9e-8eee-c7be7d1dd06a)

The Mu Editor IDE supports standard Python code and highlighted code-checking, as well as CircuitPython serial communications, REPL access, and data plotting extensions.  

After setting the Editor Mode for CircuitPython, it was very easy to cut and paste online code for a USB connected [Circuit Playground Express LED finger plethysmograph pulse detector project](https://cdn-learn.adafruit.com/downloads/pdf/make-it-pulse.pdf).
The Mu code-checking even conveniently caught some inadvertent source cut-and-paste UniCode translation errors.

Having used plethysmography many times during my behavioral neurophysiology research, I was very impressed with the stability of the basic Circuit Playground light sensor and LED signal design, the CircuitPython processing and display with Mu Editor.
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/d1efbbcb-2319-44e4-9c71-8907daa23c82)

So after many weeks working on Jetson + CircuitPython ARM32 microcontroller development, it only took a few more hours to produce a practical application showing the AGX Orin's capabilities for controlling additional USB-based laboratory physiological data acquisition subsystems.

Moving on to CircuitPython BLE heart rate monitoring on an Adafruit Clue nrf52840 (M4) microcontroller:
![image](https://github.com/user-attachments/assets/f170a46a-bfcd-49a8-bfff-f32ea9be90cc)
