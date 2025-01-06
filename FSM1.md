## <u>AI Lab Notes</u>

### **Finite-State Machine Automata and Neuromorphic Neural Networks**

[An earlier AI Lab Note](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/blob/main/GameOfLife.md) took a quick look at [cellular automata](https://plato.stanford.edu/entries/cellular-automata/) and running them on Jetson AGX Orin using CLIPS and Golly.  

We now revisit [computational automata](https://en.m.wikipedia.org/wiki/Automata_theory) in greater detail, especially finite-state machines (FSMs), because of their deep mathematical history in computer sciences and current significance for robot and autonomous machine development.

A [finite-state automaton](https://en.m.wikipedia.org/wiki/Finite-state_machine) is a theoretical, mathematical computational model defining an abstract dynamic machine that can be in only one of a finite number of functional *states* at a time.  Inputs to the machine are sensed in *events* that evoke defined *transitions* from one state to another.

FSMs can be differentiated into fundamental types based on design and functional behaviors. Two of these are [deterministic](https://en.m.wikipedia.org/wiki/Deterministic_finite_automaton) and [non-deterministic](https://en.m.wikipedia.org/wiki/Nondeterministic_finite_automaton) finite state automata, which can define equivalent alternative machines.

In a different approach for modeling quantum computing, [quantum finite automata](https://en.m.wikipedia.org/w/index.php?title=Quantum_finite_automaton) can use quantum analogs of Markov decision processes for probabilistic triggering of state transitions.

Computational automata can be ranked by their overlapping mathematical problem-solving capacities (or "computability") as depicted below.  

Automated mathematical problem solving capability is greatest in universal Turing Machines.  This hierarchy was first codified in the [Church-Turing thesis](https://plato.stanford.edu/entries/church-turing/), decades before the development of stored program executing digital computers evolved from [Alan Turing](https://en.m.wikipedia.org/wiki/Alan_Turing)'s mathematical and computational theories.

From the outset of Turing's formulations and proofs, language translation was a central focus of machine input character processing, historically employed in decrypting Nazi Enigma communications code during the Second World War.  Using FSM models in natural language front-end processing long preceded the contemporary rise of deep neural network-based large language models.

![image](https://github.com/user-attachments/assets/273a2cca-b6d2-4bb0-82e4-8b11eca86b43)

Finite-state models can be reified in dynamically operational hardware systems built with discrete electronics modules, field programmable gate arrays, or with micromputers.

#### Python Finite-State Machines on Jetson AGX Orin DK

State machine models can be created in several popular general purpose programming languages (e.g. C++, Java), and Python is most relevant to the ongoing AI Lab projects.  

[Python-statemachine](https://pypi.org/project/python-statemachine/) provides a comprehensive *pip*-accessible library of FSM-specific classes, objects, and functions for Python 3.6+ systems.  Object-oriented methods are most suited for building efficient state, transition, and event functions for such autonomous machines.

![image](https://github.com/user-attachments/assets/2d8d5e87-7576-4252-8e6c-e321c3454e13)


#### CircuitPython Finite-State Machines on ARM MCUs and neuromorphic neurons

For current AI Lab spiking neuron modeling projects using clusters of ARM32 microcontrollers, FSM model code is replacing more conventionally programmed loops and IO handler functions. These MCUs are programmed with CircuitPython using basic builtin IO classes, objects, and functions for states, transitions, and sensing.

CircuitPython developer **adafruit** has provided a helpful comparative [learning guide to programming FSMs](https://cdn-learn.adafruit.com/downloads/pdf/circuitpython-101-state-machines.pdf) for a NY-style New Year's Eve dropping ball countdown display controller. Other common PC Python examples for FSM code development have focused on autonomous machine controllers for [traffic lights](https://python-statemachine.readthedocs.io/en/latest/readme.html) and [transit turnstiles](https://github.com/cmaugg/pystatemachine).


#### Finite-State Machines and Artficial Neural Networks

https://home.csulb.edu/~cwallis/382/readings/482/mccolloch.logical.calculus.ideas.1943.pdf

https://archive.org/details/trent_0116301269779/page/n4/mode/1up

https://archive.org/details/computationfinit0000mins/page/n4/mode/1up

https://link.springer.com/chapter/10.1007/978-3-540-27835-1_18

https://page-one.springer.com/pdf/preview/10.1007/978-3-540-27835-1_18

https://www.dlsi.ua.es/~mlf/nnafmc/pbook.pdf

https://www.dlsi.ua.es/~mlf/docum/perezortiz22b.pdf

Bursting neurons

https://pmc.ncbi.nlm.nih.gov/articles/PMC8502892/pdf/fncel-15-741292.pdf
