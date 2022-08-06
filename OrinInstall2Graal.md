## <u>AI Lab Notes</u>

#### **3. Bringing up the AI Lab symbolic software suite on the Jetson AGX Orin: To Graal and beyond**
       
My turn in the great 2022 AGX Orin Developer Kit launch/delivery queue came in early June, so initial hardware/OS setup had to precede new symbolic applications installations on the latest Ubuntu 20.04 ARM/aarch64 L4T operating system.

The first big difference from prior AGX Xavier installations, when L4T and JetPack 4.# were installed via serial connection with a Xeon/Ubuntu workstation running NVIDIA SDK Manager. 

The basic Ubuntu/L4T system came preloaded on the Orin DK eMMC, and initial familiar OS configuration, networking and account setup only required keyboard, mouse, DP-capable VESA monitor on first power-up.

Then, after  
		sudo apt update && sudo apt upgrad

of this initial setup, the new JetPack 5 software suite then only required
 
 		sudo apt install nvidia-jetpack**

for direct installation.

Unfortunately, this command failed with an error when first run.

Fortunately, attention had been directed to this in the NV Developer Jetson Forums 
https://forums.developer.nvidia.com/t/apt-install-cuda-profiler-api-11-4-failed/218634/19. 

A recommended archive version number edit (34.0 to 34.1) was easily applied to setup configuration files (etc
/apt/sources.list), and the apt install of JetPack 5.0 then went as expected
       
The evolution of direct JetPack installation and upgrades from apt archives seemed worth sustaining in installing and maintaining  the Java base system. 
 
 sudo apt install default-jdk

installed aarch64 OpenJDK 11.0.14, essentially the same default Java version available for the other Jetsons under Jetpack 4.N.

Prior tested Jetson AGX Xavier aarch64 builds of CLIPS(JNI), Clojure, and ImageJ were then simply copied to the AGX Orin ~/Home, and all the Java applications performed as expected when launched.

To complete the Clojure base with a project manager, Leiningen was also installed by 

sudo ap   install leiningen

After all this AI Lab symbolic software was thoroughly tested, activities focused on running and testing the Orin-updated NVIDIA  “Hello AI World” machine vision deep learning software suite. 

Keeping with the direct-Edge software download theme,these were downloaded/cloned from https://github.com/dusty-nv/jetson-inference

All the various imagenet, segnet, and **net .cpp and .py applications examples built and tested with the available network models and data at all available ‘layer-counts’, including GoogleNet, ResNet, Inception, nd vpp...

Just WOW…

During all this, a new update of GraalVM (22.2) Community Edition (CE) was released, so the new aarch64 .jar was downloaded from Oracle https://github.com/graalvm/graalvm-ce-builds/releases/tag/vm-22.2.0 and further installed to AGX Orin ~/Home.

Looking Onward
