## <u>AI Lab Notes</u>

### Mu Editor Installation Update and pulse rate data acquisition with ARM 0+ CircuitPlayground Express

As previously noted, Adafruit has recommended using the open source Python Mu Editor with its CircuitPython enabled MCU boards, like the Grand Central M4 Express, Metro M4 Express, and Circuit Playground Express (M0+).  

The various Mu installation instructions emphasize Windows, Mac, and standard Linux (amd64) host platforms, but no specifics about ARM64/aarch64 platforms.

Online Mu documentation listed several different Linux alternatives, including GitHub source Python3 compilation, pip3 dedicated enviroment and apt installs.

As with prior projects, I tried a vanilla Ubuntu 20.04 apt install on my Xeon RTX workstation, but it proved to be very unstable.  The other alternatives were considered, but none proved satisfactory.

In further investigating the [Mu-Editor GitHub site](https://github.com/mu-editor/mu/releases), it was finally noted that a Debian standard *Mu Editor x86-64 appimage* was made available in response to user requests.  This turned out to provide a very stabile Mu Editor installation on the Ubuntu 20.04 Xeon RTX workstation.

After all this, it seemed possible that an *ARM64 Ubuntu Mu-Editor apt archive* might have been set up, so I tried a simple 

		sudo apt install mu-editor
  
on the AGX Orin.  It simply worked and was stabile!
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/cb2d70b5-bcb0-4b9e-8eee-c7be7d1dd06a)

The Mu Editor IDE supports standard Python code and highlighted code-checking, as well as CircuitPython serial communications, REPL access, and data plotting extensions.  

After setting the Editor Mode for CircuitPython, it was very easy to cut and paste online code for a [Circuit Playground Express LED plethymograph pulse detector project](https://cdn-learn.adafruit.com/downloads/pdf/make-it-pulse.pdf).
The code-checking even conveniently caught some inadvertent source cut-and-paste UniCode translation errors.
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/d1efbbcb-2319-44e4-9c71-8907daa23c82)

So after many weeks working on Jetson + CircuitPython ARM32 microcontroller development, it only took a few more hours to produce a practical application showing the AGX Orin's capabilities for controlling additional USB-based laboratory data acquisition and control subsystems.
