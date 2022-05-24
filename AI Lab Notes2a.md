## <u>AI Lab Notes</u>

#### **2.  Making symbolic AI progamming (and other things) *go-faster* on Jetsons** with *GraalVM*, its LLVM, polyglot languages resources, and GPUs

The previous [AI Lab Notes](https://www.seeedstudio.com/blog/2022/04/10/ai-lab-notes-adding-symbolic-ai-programming-to-nvidia-jetson-powered-deep-neural-network-development-systems/) covered adding *open source* symbolic programming tools to the Jetson Xavier NX L4T/Ubuntu Linux for developing new *neural-symbolic* edge applications with NVIDIA's excellent GPU-based deep neural networks resources environments.   Prime emphasis was placed on inter-operating well with NV's powerful Python deep learning resources, including Jupyter Notebooks.

We showed the latest **[CLIPS](https://sourceforge.net/projects/clipsrules/files/CLIPS/6.40/)** (C Language Interpretive Production System) ARM64 binaries running symbolic *qualitative process models* on the Jetson Xavier NX.  Their Java Native Interface (JNI) .jars execute through the Ubuntu/L4T default ARM64 *OpenJDK 11 virtual machine* (VM).  

CLIPS code like this also provides a transitional evolutionary path for symbolic programming development using **Clojure**, an evolved functional Lisp executing through Java or OpenJDK VM.

The larger software collection also supports our lab legacy 3D/4D biomedical research imaging work (e.g., NV CLARA-type dev), anatomical informatics, and molecular biology ontologies (e.g., SNOMED CT ) for *edge laboratory* environments.  *The most important related basic binary applications -- NIH's **ImageJ** and Stanford's **Protege** ontology editor, along with **Clojure** -- currently rely on the default OpenJDK VM for execution.*



**In these Notes, we move on to adding the latest high-performance OpenJDK-compatible VM** -- **[GraalVM](https://www.graalvm.org)** --  that  speeds Java source code compilation and bytecode execution, along with including a *polyglot* (multiple language) interface (*Truffle*) with a Low Level Virtual Machine (LLVM) and binary compiler.  Besides standard Java code and .jars like new Lisp programming languages *Clojure* and *Scala*, the top layer compiler also optimizes Python, JavaScript/NodeJS, Ruby, and R high-level scripts plus C, C++, and Rust code  for fast binary *native image* executables on the host operating system and processor architecture.  

See the following GraalVM software stack diagram (from [NVIDIA's Tech Blog](https://developer.nvidia.com/blog/grcuda-a-polyglot-language-binding-for-cuda-in-graalvm/)).

![Architecture-of-grCUDA-in-the-GraalVM-Stack](https://user-images.githubusercontent.com/71346897/170131013-0a28c820-ba66-4b73-b28a-ad771d925639.png)


[Java owner Oracle developed and continues to release new GraalVM upgrades](https://medium.com/graalvm/graalvm-22-1-developer-experience-improvements-apple-silicon-builds-and-more-b7ac9a0f6066).  It ultimately collaborated with NVIDIA to develop the [grCUDA](https://github.com/NVIDIA/grcuda) polyglot bindings library 'top layer' for <u>compiling GPU kernel code with any supported language</u>.

Having missed the joint presentation on GraalVM and grGUDA at GTC 2020, I checked out [NVIDIA's archives](https://resources.nvidia.com/events/GTC2020s21269?lx=RowcGr&contentType=Demo) and reviewed the [presentation video](https://developer.nvidia.com/gtc/2020/video/s21269-vid).  

The very next step was to download the newest 22.1 release of [GraalVM Community Edition ARM64 binaries](https://www.graalvm.org/downloads/) and [grCUDA](https://github.com/NVIDIA/grcuda).



**<u>Some Immediate results after simple [installation](https://www.graalvm.org/java/quickstart/) on a Jetson AGX Xavier 32GB Dev Kit</u>**

The screen capture image below demonstrates GraalVM running the previously featured symbolic gene regulation modeling system in **CLIPS** (JNI/IDE; top center/right), together with the **ImageJ** biomedical image processing app (left) and the **Clojure** programming language REPL in a terminal window (lower center).  

![Screenshot from 2022-05-09 20-35-15](https://user-images.githubusercontent.com/71346897/170131257-44aa3557-aea2-4a2c-a4bc-d4e4740ac8a9.png)


Each of these independent Java processes was launched and tested from its own Terminal shell, with individual sessions I/O shown in the screen image.  

Starting on the left bottom, PATH and JAVA_HOME are first set to the GraalVM installation */bin* directory, so ImageJ.jar will use GraalVM.  After launch, a digital pathology slide is loaded through the GUI File menu, displaying a microscopic image of stained gland tissues, from those used in our CLARA AGX research and development.

In the middle-bottom shell, with JAVA_HOME/PATH set to 'graal/bin', *clojure .jar*  is launched from its own download directory, and the Clojure command line REPL awaits code input at the *user=>* prompt.

Finally in the lower right shell, with settings made, CLIPSJNI IDE launches with GraalVM,  then the system  loads and runs the *regulation6.clp* gene regulation process experiment.



**Execution speed considerations, grCUDA examples and TensorRT**

As expected, all .jar applications peformed well using GraalVM, and each first launch was prompt without exceptional delay.  Without time data, it's difficult to report that each app launched faster than with the default-jdk.  However, the Oracle team has already provided some more meaningful technical facts and [execution speed increase data for GraalVM 22.1](https://medium.com/graalvm/graalvm-22-1-developer-experience-improvements-apple-silicon-builds-and-more-b7ac9a0f6066).  

Perhaps more important than simple program launch speed, **grCUDA** and GraalVM open up *direct GPU kernel processing* to binaries compiled from the more popular programming languages, as thoroughly presented in Dr. Rene Mueller's 2019  [NVIDIA Tech Blog](https://developer.nvidia.com/blog/grcuda-a-polyglot-language-binding-for-cuda-in-graalvm/).  

Most remarkable for this developer was the downloaded grCUDA /examples [file that uses TensorFlow](https://github.com/NVIDIA/grcuda/tree/master/examples/tensorrt) to train an [LeNet5 model](https://towardsdatascience.com/understanding-and-implementing-lenet-5-cnn-architecture-deep-learning-a2d531ebc342) with the MNIST  handwriting dataset.  Then, an inference engine is generated from the model using Tensor RT and Python scripting. Finally the engine is instantiated from a Node.js script through grCuda.

For projects aimed at neural-symbolic development, this opens up polyglot code pipelining and GPU-accelerated convolutional network model training and enhanced symbolic inference with NVIDIA  Jetsons!  It is also a testament to the amazing collaborative open source work by many gifted developers over the years in the Jetson developer environment.

At let us not forget that NVIDIA finally [announced open-sourcing their Linux GPU drivers](https://www.phoronix.com/scan.php?page=article&item=nvidia-open-kernel&num=1) this month:  More GPU power now, in whatever language(s) work(s) best for the task.

Stay tuned! 

 Robert B. Trelease, Ph.D.  05/17/2022
