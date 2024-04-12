## <u>AI Lab Notes</u>

#### ***Chats with*** **Doctor Anatomy -** ***Core anatomical knowledge in an open source large language model***

##### Current Context

[Earlier AI Lab Notes](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/blob/main/Onto1.md) and linked [publications](https://anatomypubs.onlinelibrary.wiley.com/doi/10.1002/ar.b.20095) have covered legacy symbolic programming approaches to anatomical reasoning prior to the rise of deep neural networks (DNNs).  

Having been a research university neuroscientist and developer through prior decades of *symbolic* AI -- with their emphases on expert knowledge bases, ontologies, logical inference engines, and explanability of inferential logic -- there were some more recent moments of cognitive dissonance on seeing DNN output processes newly (re)defined as [*inferences*](https://research.ibm.com/blog/AI-inference-explained).

And no, current DNNs aren't really "neural" or fundamentally inspired by deep knowledge of human brain functional anatomy, BUT [***eppur si muove***](https://en.m.wikipedia.org/wiki/And_yet_it_moves), they work and do some remarkable computing tasks! 

So the philological aspects of reconciling AI legacy symbolic and DNN/ML inferencing connotations are best left to historians and authors of [artificial intelligence textbooks](http://aima.cs.berkeley.edu/index.html).

My tasks as a neuroscientist building and using AI tools have simply involved accomodation, adaptation, redefinition, and repurposing of new information, familiar real human biological brain functions. 

Thus, a practical major theme of these AI Lab Notes has been addressing some currently perceived shortcomings of DNN-based AI applications, including explainability of inferences, with hybrid neural-symbolic programming.
Beyond that, programmatic neuromorphic modeling also studies more functionally rigorous biological *neuronal* network designs, since human brain cognitive mechanisms naturally support thinking with apparently conflicting concepts.

*In the currently reported study, we investigate the quality of anatomical information contained in and accessed by a major Open Source DNN-based large language model, operating with a web-accessed "chatbot" user interface*.  

Chatbots (e.g., ChatGPT) have been popularly adopted for educational applications like essay writing, raising many reasonable concerns and issues for students, teachers, and administrators.  From an adoption perspective, in the context of adult self-directed learning, there are basic questions about the accuracy, validity and biases of information communicated by potential 'AI tutors'.

[This month's Communications of the ACM](https://m-cacm.acm.org/magazines/2023/12/278146-thus-spake-chatgpt/fulltext) has a particularly timely opinion column on explanatory reasoning shortcomings demonstrated by ChatGPT, by Subhabrata Dutta and Tanmoy Chakraborty.  They illustrate a problematic conversation exploring technical aspects of fractals, with the chatbot in the role of generating explanations for a non-technical user.

In the continuing context of [prior work on anatomical reasoning](https://anatomypubs.onlinelibrary.wiley.com/doi/10.1002/ar.b.20095), this AI Lab Note considers a similar chatbot explanatory function relative to its usefulness for medical student instruction.   In the current trials, an experienced medical school anatomist asks questions of the chatbot, much like a individual student "practical exam", to assess breadth, depth, accuracy, and appropriateness of the *inferred* anatomical knowledge communicated.  

In essence, to return to the earlier neural versus symbolic reasoning dichotomy, this asks if the LLM inferences are consistently valid.

#### Materials and Methods

**The current series of *anatomical reasoning* experiments** uses new Open Source LLMs provided in the comprehensive [NVIDIA Jetson Generative AI Lab](https://www.jetson-ai-lab.com/index.html) containers.  For the first trials, we created a chatbot character called **Doctor Anatomy** to answer questions about breadth and depth of anatomical knowledge contained in a LLaMa 2 Chat model.



**Platform:** NVIDIA Jetson AGX Orin 64GB, booting 1 TB NVMe SSD with JetPack 5.1.2, Linux (L4T r35.4.1); [NVIDIA Jetson Generative AI Lab](https://www.jetson-ai-lab.com/tutorial_text-generation.html) text-generation-webui container; [LLaMa-2-7B-Chat-GGUF model](https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGUF) 

***Figure 1.*** Some fair responses to questions on cardiac anatomy and innervation in the initial Jetson Chatbot (localhost) session.
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/90e79657-820c-465f-be94-d26098840e51)

***Figure 2.*** Localhost *LLama-2-7B-Chat* **web UI** on FireFox (l) and the Stanford Protege ontology application (r) accessing The Foundational Model of Anatomy (FMA.owl) ontology concurrently on the AGX Orin host.  Doctor Anatomy wrongly attributes FMA creation to the American Association of Anatomists and misstates the proper functional organization.
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/c1c02ff9-6f30-41f5-b4be-a279a273764e)


***Figure 3.*** In another later iPad wifi session, the chatbot prevaricates further on who developed The Foundational Model of Anatomy and obfuscates the proper role of principal investigator and leading contributor Cornelius Rosse.
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/021c93c8-74c2-488d-8e33-adf68bb33eea)


***Figure 4.*** In yet another later AGX Orin localhost session, the chatbot prevaricates more on FMA and Cornelius Rosse's roles, while Protege displays the FMA editor and creator metadata.
![image](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/assets/71346897/65992b63-e099-4ba1-b4af-cb8b1dfbc47e)

...

*Fair disclosure*: The author has been a career member of the American Association of Anatomists, worked extensively with original MySQL and current OWL versions of FMA, and personally interacted with Cornelius Rosse and his UWash Structural Informatics Group members over more than two decades.

*In progress,* to be continued.
