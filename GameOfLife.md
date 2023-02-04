## <u>AI Lab Notes</u>

### **Cellular automata, Turing machines, and the *Game of Life* with Jetson CLIPS**

When topic map maven and wise dev friend Jack Park first alerted me to [Ryjo's new simple CLIPS implemention](https://ryjo.codes/articles/conways-game-of-life-written-in-clips.html) of [John Horton Conway](https://en.wikipedia.org/wiki/John_Horton_Conway)'s ***Game of Life***, that blog's in-text code was copied and assemble-edited **ASAP**. 

After all, it just ***had*** to work on the AGX Orin with the latest ARM/aarch64 Ubuntu binaries of CLIPS 6.4 in the AI Lab Jetson software suite. And so many of our efforts had been directed at modeling and simulation, with ***cellular automata*** like *Life* being exemplary rule-based process models...

Sure enough, it was a pretty straightforward task to make basic *Life* run from a CLIPS terminal command line (see image at bottom).  

Thanks Ryjo!  And yet again, thanks Jack!

Rather than carry on too much more about the significance of cellular automata, like Conway's Game of Life, in mathematics and computational theory, rule-based processing, and instantiating *Turing machines*, links are provided below to some very well written references covering the most important details.

Many devoted researchers and developers have contributed to growing community resources for these dynamic 'a-life' models, automata such as glider-guns, pulsars, and rock-paper-scissors.

[Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)

[Cellular Automata, according to Stanford Encyclopedia of Philosophy](https://plato.stanford.edu/entries/cellular-automata/)

[Cellular Automaton @ ConwayLife wiki](https://conwaylife.com/wiki/Cellular_automaton)

[A Clojure implementation of Game of Life](https://github.com/gbouknecht/conway-life)

[**Efficient execution of cellular automata on GPU**](https://www.sciencedirect.com/science/article/pii/S1569190X22000259)

 

##### =---> To get [golly](https://sourceforge.net/projects/golly/0), a full-featured Game of Life + cellular automata GUI app, do ***sudo apt install golly*** on workstation or Jetson.

[GollyGang Rule Table Repository for Cellular Automata](https://github.com/GollyGang/ruletablerepository)



=---> CW from upper left: oscillators, pulsar, gliders, and simple space ship automata
![Screenshot from 2023-01-21 17-26-37](https://user-images.githubusercontent.com/71346897/213896784-2693497e-cea8-4ba7-b85e-a4264654a1b9.png)

=---> Golly on the AGX Orin: Fireworks, a Generations cellular automaton after 2188 generations
![Screenshot from 2023-02-03 15-58-11](https://user-images.githubusercontent.com/71346897/216734608-39ca0b72-eaea-488e-8e59-919ffef38566.png)

RBT 01/21/2023
