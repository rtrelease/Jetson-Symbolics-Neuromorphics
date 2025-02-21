## <u>AI Lab Notes</u>

### **Finite-State Machine Automata and Neuromorphic Neural Networks**

[An earlier AI Lab Note](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/blob/main/GameOfLife.md) took a quick look at [cellular automata](https://plato.stanford.edu/entries/cellular-automata/) and running them on Jetson AGX Orin using CLIPS and Golly.  

We now revisit [computational automata](https://en.m.wikipedia.org/wiki/Automata_theory) in greater detail, especially finite-state machines (FSMs), because of their deep mathematical history in computer sciences and current significance for robot and autonomous machine development.

A [finite-state automaton](https://en.m.wikipedia.org/wiki/Finite-state_machine) is a theoretical, mathematical computational model defining an abstract dynamic machine that can be in only one of a finite number of functional *states* at a time.  Inputs to the machine are processed in *events* that evoke defined *transitions* from one state to another.

FSMs can be differentiated into fundamental types based on design and functional behaviors. Two of these are [deterministic](https://en.m.wikipedia.org/wiki/Deterministic_finite_automaton) and [non-deterministic](https://en.m.wikipedia.org/wiki/Nondeterministic_finite_automaton) finite state automata, which can define equivalent alternative machines.

In a different approach for modeling quantum computing, [quantum finite automata](https://en.m.wikipedia.org/w/index.php?title=Quantum_finite_automaton) can use quantum analogs of Markov decision processes for probabilistic triggering of state transitions.

Isolated automata can function alone, and multiple FSMs can interact in networks, as will be considered later in this Lab Note.

Computational automata as a whole can be ranked by their overlapping mathematical problem-solving capacities (or "computability") as depicted below.  

Automated mathematical problem solving capability is greatest in universal Turing Machines.  This hierarchy was first codified in the [Church-Turing thesis](https://plato.stanford.edu/entries/church-turing/), decades before the development of stored program executing digital computers evolved from [Alan Turing](https://en.m.wikipedia.org/wiki/Alan_Turing)'s computational theories and mathematical proofs.

![image](https://github.com/user-attachments/assets/273a2cca-b6d2-4bb0-82e4-8b11eca86b43)
 => [Wikimedia Commons image](https://en.m.wikipedia.org/wiki/File:Automata_theory.svg)

From the inception of Turing's formulations and proofs, language translation was a central focus for machine input character processing, historically employed in decrypting Nazi Enigma communications code during the Second World War.  So using FSM models in natural language front-end processing long preceded the contemporary rise of deep neural network-based large language models.

Finite-state models can be *reified* in dynamically operational hardware systems built with mechanical parts, discrete electronics modules, field programmable gate arrays, or with micromputers.

#### Python Finite-State Machines on Jetson AGX Orin DK

State machine models can be created in several popular general purpose programming languages (e.g. C++, Java), and Python is most relevant to the ongoing AI Lab projects.  

[Python-statemachine](https://pypi.org/project/python-statemachine/) is a *pip*-installable library of FSM-specific classes, objects, and functions for Python 3.6+ systems.  Object-oriented methods are most suited for building efficient state, transition, and event functions for such autonomous machines.

![image](https://github.com/user-attachments/assets/2d8d5e87-7576-4252-8e6c-e321c3454e13)


#### CircuitPython Finite-State Machines on ARM MCUs and neuromorphic neurons

For current AI Lab spiking neuron modeling projects using clusters of ARM32 microcontrollers, FSM model code is replacing more conventionally programmed loops and IO handler functions. These MCUs are programmed with CircuitPython, using basic builtin IO classes, objects, and functions for states, transitions, and sensing events.

CircuitPython developer **adafruit** has provided a helpful comparative [learning guide to programming FSMs](https://cdn-learn.adafruit.com/downloads/pdf/circuitpython-101-state-machines.pdf) for a NY-style New Year's Eve dropping ball countdown display controller. Other common PC Python examples for FSM code development have focused on autonomous machine controllers for [traffic lights](https://python-statemachine.readthedocs.io/en/latest/readme.html) and [transit turnstiles](https://github.com/cmaugg/pystatemachine).


#### Finite-State Machines and Artificial Neural Networks: Minsky (1967) Reconsidered

Finite-state machine models were closely linked to the development of artificial neural networks theories early in the history of digital computing and scholarly artificial intelligence research.

In their seminal 1943 work on logic processing capabilities in artificial neural networks, [McCulloch & Pitts](https://home.csulb.edu/~cwallis/382/readings/482/mccolloch.logical.calculus.ideas.1943.pdf) presented a finite state model of on/off neurons linked by thresholded synapses somewhat comparable with those of biological neurons.

Marvin Minsky's foundational 1967 text [Computation: Finite and Infinite Machines](https://archive.org/details/computationfinit0000mins/page/n4/mode/1up) extended McCulloch and Pitts figurative artifical neuronal networks into larger layered structured models that could perform numerous logical and mathematical functions.

Remarkably, Minsky noted at the outset of this seminal work (and during years of MIT EE lectures it formalized) that his abstract binary (on/off) FSM 'neurons' were *not* intended to represent biological nerve cell behaviors.  The focus was rather on demonstrating mathematical proofs that networks of such artificial neurons could perform numerical and logical computations.

Such artificial neural networks were represented by interconnected binary FSMs with off and on states, as depicted below.


#### Beyond On/off Neuron Models: To Resting, Spiking, Bursting, Transducing, and Multi-State Dynamics

In the eight decades since McCulloch and Pitts, neuroscienctific knowledge about neuronal biology, functional brain anatomy, and cognitive behaviors increased vastly.

+++

Review of bursting neuron mechanisms and types:
https://pmc.ncbi.nlm.nih.gov/articles/PMC8502892/pdf/fncel-15-741292.pdf



http://www.izhikevich.org/publications/spikes.htm
![image](https://github.com/user-attachments/assets/634543b3-9253-41a0-9324-da2c5341b26c)

#### Modeling Different CNS Neuron Types with Non-binary FSMs: Spiking through Bursting Behaviors

So rather than proceeding with binary state FSMs for building out operational artifical *neuronal networks* based on canonical microcircuit models, trinary state neurons made a good focus for encompassing known resting, spiking, and bursting behaviors.

![image](https://github.com/user-attachments/assets/e4e41245-6b2c-46d6-8023-b93109928175)

![image](https://user-images.githubusercontent.com/71346897/213343140-41049d4a-09e4-4563-a68f-a6e6db5b944f.png)

https://archive.org/details/trent_0116301269779/page/n4/mode/1up

https://link.springer.com/chapter/10.1007/978-3-540-27835-1_18

https://page-one.springer.com/pdf/preview/10.1007/978-3-540-27835-1_18

https://www.dlsi.ua.es/~mlf/nnafmc/pbook.pdf

https://www.dlsi.ua.es/~mlf/docum/perezortiz22b.pdf

In media res...
