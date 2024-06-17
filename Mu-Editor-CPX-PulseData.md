## <u>AI Lab Notes</u>

### Mu Editor Installation Update and pulse rate data acquisition with ARM 0+ CircuitPlayground Express

As previously noted, Adafruit has recommended using the open source Python Mu Editor with its CircuitPython enabled MCU boards, like the Grand Central M4 Express, Metro M4 Express, and Circuit Playground Express (M0+).  

Their various Mu installation instructions emphasize Windows, Mac, and standard Linux (amd64) host platforms, but no specifics about ARM64/aarch64 platforms.

Online Mu documentation listed several different methods, including GitHub source Python3 compilation, pip3 dedicated enviroment and apt installs.

As with prior projects, I tried a vanilla Ubuntu 20.04 apt install on my Xeon RTX workstation, but it proved to be very unstable.  The other alternatives were considered, but none proved satisfactory.

In investiagation the Mu-Editor GitHub site, it was finally noted that a Debian standard Mu Editor appimage was made available in response to user requests.

![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/cb2d70b5-bcb0-4b9e-8eee-c7be7d1dd06a)

![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/d1efbbcb-2319-44e4-9c71-8907daa23c82)
