## <u>AI Lab Notes</u>

### **Finite-State Machine Automata and Neuromorphic Neural Networks**

[An earlier AI Lab Note](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/blob/main/GameOfLife.md) took a quick look at [cellular automata](https://plato.stanford.edu/entries/cellular-automata/) and running them on Jetson AGX Orin using CLIPS and Golly.  

We now revisit [computational automata](https://en.m.wikipedia.org/wiki/Automata_theory) in greater detail, especially finite-state machines (FSMs), because of their deep mathematical history in computer sciences and current significance for robot and autonomous machine development.

A [finite-state automaton](https://en.m.wikipedia.org/wiki/Finite-state_machine) is a theoretical mathematical computational model defining an abstract machine that can be in only one of a finite number of functional states at a time.  *Inputs* to the machine evoke defined *transitions* from one state to another.

FSMs can be differentiated into fundamental types based on design and functional behaviors. Two of these are [deterministic](https://en.m.wikipedia.org/wiki/Deterministic_finite_automaton) and [non-deterministic](https://en.m.wikipedia.org/wiki/Nondeterministic_finite_automaton) finite state automata, which can define equivalent alternative machines.

In a different approach for modeling quantum computing, [quantum finite automata](https://en.m.wikipedia.org/w/index.php?title=Quantum_finite_automaton) can use quantum analogs of Markov decision processes for probabilistic triggering of state transitions.

Computational automata can be ranked by their overlapping mathematical problem-solving capacities (or "computability") as depicted below.  Automated mathematical problem solving capability is greatest in universal Turing Machines, and this hierarchy was first codified in the [Church-Turing thesis](https://plato.stanford.edu/entries/church-turing/), decades before the development of digital computers derived from Turing theories.

![image](https://github.com/user-attachments/assets/273a2cca-b6d2-4bb0-82e4-8b11eca86b43)

#### Python Finite-State Machines on Jetson AGX Orin DK

State machines can be created in several popular general purpose programming languages, and most relevant to the AI Lab projects is Python.  

[Python-statemachine](https://pypi.org/project/python-statemachine/) provides a comprehensive *pip*-accessible library of FSM-specific classes, objects, and functions for Python 3.6+ systems.  Object-oriented methods are most suited for building efficient states and transitions for such autonomous machines.

![image](https://github.com/user-attachments/assets/2d8d5e87-7576-4252-8e6c-e321c3454e13)


#### CircuitPython Finite-State Machines on ARM MCUs and neuromorphic neurons

For current AI Lab spiking neurons projects using multiple ARM32 microcontrollers, FSM code is replacing more conventionally programmed loops and IO handler functions. These MCUs are programmed in CircuitPython using basic builtin IO classes, objects, and functions for states and transitions.

CircuitPython developer **adafruit** has provided a helpful comparative [learning guide to programming FSMs](https://cdn-learn.adafruit.com/downloads/pdf/circuitpython-101-state-machines.pdf) for a NY-style New Year's Eve dropping ball countdown display controller. Other common PC Python examples for FSM code development have focused on autonomous machine controllers for [traffic lights](https://python-statemachine.readthedocs.io/en/latest/readme.html) and [transit turnstiles](https://github.com/cmaugg/pystatemachine).

#### Finite-State Machines and Artficial Neural Networks

