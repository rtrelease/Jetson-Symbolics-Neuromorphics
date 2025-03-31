## <u>AI Lab Notes</u>

### **Owlready2 revisited: Python programming and integration of OWL ontologies**

Earlier [Notes](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/blob/main/Onto1.md) considered the use of ontologies in conjunction with medical imaging, computer vision, and large language model applications.

[Owlready2](https://github.com/pwin/owlready2/tree/master) is a [pip installable](https://pypi.org/project/owlready2/) Python module for programming with OWL (web ontology language) encoded ontologies. It is compatible with Linux Python version 3.6 on, including aarch64 ARM systems.

Developer Jean-Baptiste Lamy, a French medical informaticist, published a major scientific [article](http://www.lesfleursdunormal.fr/_downloads/article_owlready_aim_2017.pdf) about using it with [Gene Ontology](https://geneontology.org) and automating the comparison of drug contraindications.

Considering this as a starting point for a new Owlready2 installation on an AGX Orin (JP 5.1.2), the following image shows a few lines of Python in a simple Jupyter Notebook accessing a local subset of the Gene Ontology *(go)*.  To the left, Protege displays an expanded entity-based IDEvview of go-basic.owl.
![image](https://github.com/user-attachments/assets/6c88de59-2991-419f-abc8-972eaf3ab679)
