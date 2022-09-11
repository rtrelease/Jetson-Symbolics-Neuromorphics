## <u>AI Lab Notes</u>

#### **3. Bringing up the AI Lab symbolic software suite on the Jetson AGX Orin DK: To Graal and beyond**
       
My turn in the great 2022 AGX Orin Developer Kit launch delivery queue came in early June, so initial hardware/OS setup had to precede new symbolic applications installations on the latest Ubuntu 20.04 ARM/aarch64 L4T operating system.

The first big difference from prior AGX Xavier installations, when L4T and JetPack 4.# were installed via serial connection with a Xeon/Ubuntu workstation running NVIDIA SDK Manager:

*The basic Ubuntu/L4T operating system came preloaded on the Orin DK eMMC, and initial familiar OS configuration, networking and account setup only required keyboard, mouse, and DP-capable VESA monitor on first power-up.*

So after

		sudo apt update && sudo apt upgrade

of this initial setup, the new JetPack 5 software suite required only
 
 
		sudo apt install nvidia-jetpack

for direct installation.

*Unfortunately, this install command failed on a script file load url error when first run.*

Fortunately, early attention had been directed to this in the NV Developer Jetson Forums 
https://forums.developer.nvidia.com/t/apt-install-cuda-profiler-api-11-4-failed/218634/19. 

A recommended archive version number edit (34.0 to 34.1) was easily applied to setup configuration files (etc/apt/sources.list), and the apt install of JetPack 5.0 then went as expected
       
The evolution of edge-direct NVIDIA JetPack installation and upgrades from apt archives seemed worth supporting by apt installing and maintaining the symbolic proramming systems' Java VM base system - 
 
		sudo apt install default-jdk

initially installed aarch64 OpenJDK 11.0.14, essentially the same default Java version available for the other Jetsons under Jetpack 4.N.

Tested Jetson AGX Xavier aarch64 build directories for CLIPS(JNI), Clojure, ImageJ, and Protege were then simply USB-clip-copied to the AGX Orin ~/Home, and all the Java applications performed as expected when launched.

To complete the Clojure base with a project manager, Leiningen was also installed by 

		sudo apt install leiningen

After all this AI Lab symbolic software was thoroughly run-tested, activities focused on running and testing the Orin-updated NVIDIA “Hello AI World” machine vision deep learning software suite. It made for very busy weeks!

Keeping with the new NVIDIA direct-to-edge device software download theme, all the newest trusty Dusty source code was downloaded/cloned on the AGX Orin DK from https://github.com/dusty-nv/jetson-inference.

All the various imagenet, segnet, posenet, SSD-Mobilenet, Cityscapes, DeepScene networks plus .cpp and .py applications examples were then built and tested on the Orin with the available network models and data at all available ‘layer-counts’, including GoogleNet, ResNet, Inception-v4, and VGG.

  *--| Running image recognition with ResNet-18 on sample data plus my own hummer |--*
![image](https://user-images.githubusercontent.com/71346897/183269986-70e0d642-5e32-4cd9-a05e-4cdc10c507d4.png)

WOW…


Sometime during all this Orin net-building *flow*, a new 22.2 update of GraalVM Community Edition (CE) was released. So its new aarch64 .jar was soon downloaded from Oracle at https://github.com/graalvm/graalvm-ce-builds/releases/tag/vm-22.2.0, and further installed to its own Orin directory under ~/Home.

It has been particularly instructive to use the new **native-image** GraalVM extension to compile fast Linux native apps from existing jars.  This and other language and LLVM compiler Graal extension modules provide vital interoperability for a new polyglot neurosymbolic development environment growing out of established Jetson .py and .cpp deep neural net resources.

 --| Running ImageJ and displaying a DICOM MRI stack using GraalVM 22.2 |--
![imageJ](https://user-images.githubusercontent.com/71346897/183269422-764967e2-0585-47ea-bf83-8ecf548a85bc.png)

***Looking Onward***

Although our Jetson Xaviers have been tested successfully with other implementations of Lisp for symbolic programming, including eMACS Lisp, Armed Bear Common Lisp (ABCL), and considered with others using JVMs for execution like Kotlin, we have settled on Clojure. 

Clojure provides the best direct migration pathway for evolving the simplified framework of our published symbolic molecular processes modeling system environment now running with updated Lisp-like CLIPS production rules.

For those special needs for legacy AI logic programming, SWI-Prolog and SWI-Prolog-java bindings can also be apt installed on AGX Orin.  We had no difficulties with the installation, but we are leaving it alone at present.

We are also currently reviewing [Neuro-Symbolic Artificial Intelligence: The State of the Art](https://ebooks.iospress.nl/ISBN/978-1-64368-245-7), from IOS Press. This has provided historical research backgrounds and important insights on evolving newer methods for integrating symbolic reasoning and semantic knowledge with current GPU-based deep neural networks and machine learning systems on HPC edge systems.

....

RBT 08/06/2022

