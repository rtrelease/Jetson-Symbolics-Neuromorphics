## <u>AI Lab Notes</u>

#### AGX Thor Symbolics + Neomorphics Software Installations

**Foundational Installations for symbolic, neurosymbolic, neuromorphic computing, and MCU I/O extensions of AGX Orin base software**

 - *OpenJDK* Java Base -  *sudo apt install default-jdk*  **= Java 21**

 - *Protege* Desktop Ontology Editor - platform independent download site: https://protege.stanford.edu/software.php
   - requires JAVA_HOME setting, eg: *export JAVA_HOME=usr/lib/jvm/java-11-openjdk-arm64*

- *owlready2* - Python library for integrating OWL Ontologies: https://pypi.org/project/owlready2/ - *sudo pip install owlready2*

 - *CLIPS* symbolic programing language - compilable 6.41 binaries/source (follow Linux build instructions): https://sourceforge.net/projects/clipsrules/files/CLIPS/6.4.1/
   
 - *Arduino IDE 1.8.19 (ARM64)* - https://downloads.arduino.cc/arduino-1.8.19-linuxaarch64.tar.xz
 
 - *Jupyter* - *pip* base system install: https://learn.adafruit.com/circuitpython-with-jupyter-notebooks/installing-on-mac-linux

 - *CircuitPython* - base Jupyter kernel library install from github: https://learn.adafruit.com/circuitpython-with-jupyter-notebooks/installing-on-mac-linux

**Scientific Image Processing**
 - *ImageJ* - [Legacy application from the NIH/NSF](https://imagej.net/) - Use platform independent jar with existing JDK installation and/or GraalVM


**CircuitPython Editor/IDE with serial communications and plotter**
 - *Mu Editor* - [Mu Editor Documentation](https://mu.readthedocs.io/_/downloads/en/latest/pdf/) - *sudo apt install mu-editor*


**Cellular Automata**

 - *Golly* - Game of Life automata app: *sudo apt-get -y install golly*
   
**Finite-State Machines**

 - *Python State Machine library* pip install: https://pypi.org/project/python-statemachine/: *sudo pip install python-statemachine*

**Scientific Programming Language** - MatLab compatible

 - *GNU Octave* - https://wiki.octave.org/Octave_for_Debian_systems:    *sudo apt install octave*

**Large-scale biological neuron network modeling including spiking neurons**
 - *Nengo* large scale brain modeling in Python - https://github.com/nengo/nengo: *sudo pip install nengo*

