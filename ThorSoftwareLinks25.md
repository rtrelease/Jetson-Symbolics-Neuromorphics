## <u>AI Lab Notes</u>

#### AGX Thor Symbolics + Neomorphics Software Installations

**Foundational Installations for symbolic, neurosymbolic, neuromorphic computing, and MCU I/O extensions for AGX Thor, JetPack 7 base software**

 - +*OpenJDK* Java Base -  *sudo apt install default-jdk*  **= Java 21**

 - +*Protege* Desktop Ontology Editor - platform independent download site: https://protege.stanford.edu/software.php
   - **requires JAVA_HOME setting:**
   - *export JAVA_HOME=usr/lib/jvm/java-21-openjdk-arm64*
   - *export PATH=$PATH:$JAVA_HOME/bin*

- *owlready2* - Python library for integrating OWL Ontologies: https://pypi.org/project/owlready2/ - *sudo pip install owlready2*

 - +*CLIPS* symbolic programing language - compilable 6.41 binaries/source (follow Linux build instructions): https://sourceforge.net/projects/clipsrules/files/CLIPS/6.4.1/ - CLIPSJNI launch: java -Djava.library.path= -jar CLIPSIDE.jar
   
 - +*Arduino IDE 1.8.19 (ARM64)* - https://downloads.arduino.cc/arduino-1.8.19-linuxaarch64.tar.xz
 
 - *Jupyter* - part of JetPack 7 BSP .  Otherwise *pip* base system and conda installs;

 - *CircuitPython* - base Jupyter kernel library install from github: https://learn.adafruit.com/circuitpython-with-jupyter-notebooks/installing-on-mac-linux

**Scientific Image Processing**
 - +*ImageJ* - [Legacy application from the NIH/NSF](https://imagej.net/) - Use platform independent jar with existing JDK installation and/or GraalVM, or -  *sudo apt install imagej*


**CircuitPython Editor/IDE with serial communications and plotter**
 - *Mu Editor* - sundowned by developer
 - *Thonny* - [basic Python editor compatible with CP](https://thonny.org) - install version 4.0.1 from Jetson/Ubuntu Software app ***freezes on open new document***


**Cellular Automata**

 - +*Golly* - Game of Life automata app: *sudo apt install golly*
   
**Finite-State Machines**

 - *Python State Machine library* pip install: https://pypi.org/project/python-statemachine/: *sudo pip install python-statemachine*

**Scientific Programming Language** - MatLab compatible

 - +*GNU Octave* - https://wiki.octave.org/Octave_for_Debian_systems:    *sudo apt install octave*

**Large-scale biological neuron network modeling including spiking neurons**
 - *Nengo* large scale brain modeling in Python - https://github.com/nengo/nengo: *sudo pip install nengo*

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d7348185-10ce-4232-910d-b1bef089c9fa" />
