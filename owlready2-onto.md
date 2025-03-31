## <u>AI Lab Notes</u>

### **Owlready2 revisited: Python programming and integration of OWL ontologies**

Earlier [Notes](https://github.com/rtrelease/Jetson-Symbolics-Neuromorphics/blob/main/Onto1.md) considered the use of ontologies in conjunction with medical imaging, computer vision, and large language model applications.

[Owlready2](https://github.com/pwin/owlready2/tree/master) is a [pip installable](https://pypi.org/project/owlready2/) Python module for programming with OWL (web ontology language) encoded ontologies. It is compatible with Linux Python version 3.6 on, including aarch64 ARM systems.

Developer Jean-Baptiste Lamy, a French Informaticit, published a major scientific [artical](http://www.lesfleursdunormal.fr/_downloads/article_owlready_aim_2017.pdf) about using it with Gene Ontology to study cancer literature,

This shows using Owlready2 with a simple Jupyter Notebook to access a local subset of the Gene Ontology on AGX Orin (JP 5.1.2).  To the left, Protege displays an expanded entity-based view of go-basic.owl.
![image](https://github.com/user-attachments/assets/6c88de59-2991-419f-abc8-972eaf3ab679)
