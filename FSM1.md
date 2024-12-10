## <u>AI Lab Notes</u>

### **Finite-State Machine Automata and Neuromorphic Neural Networks**

[An earlier AI Lab Note](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/blob/main/GameOfLife.md) took a quick look at [cellular automata](https://plato.stanford.edu/entries/cellular-automata/) and running them on Jetson AGX Orin using CLIPS and Golly.  

We now revisit [computational automata](https://en.m.wikipedia.org/wiki/Automata_theory) in greater detail, especially finite-state machines (FSMs), because of their deep mathematical history in computer sciences and significance to robot and autonomous machine development.

A [finite-state automaton](https://en.m.wikipedia.org/wiki/Finite-state_machine) is a theoretical mathematical computational model defining an abstract machine that can be in only one of a finite number of functional states at a time.  *Inputs* to the machine evoke defined *transitions* from one state to another.

FSMs can be differentiated into fundamental types based on design and functional behaviors. Two of these are [deterministic](https://en.m.wikipedia.org/wiki/Deterministic_finite_automaton) and [non-deterministic](https://en.m.wikipedia.org/wiki/Nondeterministic_finite_automaton) finite state automata, which can define equivalent alternative machines.

In a different approach for modeling quantum computing, [quantum finite automata](https://en.m.wikipedia.org/w/index.php?title=Quantum_finite_automaton) can use quantum analogs of Markov decision processes for probabilistic triggering of state transitions.

Computational automata can be ranked by their mathematic problem-solving capacity as depicted below.  Mathematical problem solving capability is greatest in Universal Turing Machines.
https://plato.stanford.edu/entries/church-turing/



![image](https://github.com/user-attachments/assets/273a2cca-b6d2-4bb0-82e4-8b11eca86b43)
.

#### Python Finite-State Machines on Jetson AGX Orin DK

https://pypi.org/project/python-statemachine/

![image](https://github.com/user-attachments/assets/4f97ce3a-1abe-452b-a8e3-876096116625)


#### CircuitPython Finite-State Machines on ARM MCUs and neuromorphic neurons

https://cdn-learn.adafruit.com/downloads/pdf/circuitpython-101-state-machines.pdf
