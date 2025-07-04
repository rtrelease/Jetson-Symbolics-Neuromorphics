### ImageJ and connecting computer vision with deep learning

[The ImageJ ecosystem for processing, visualization, and analysis](https://pmc.ncbi.nlm.nih.gov/articles/PMC7737784/pdf/PRO-30-234.pdf)

[Deep ImageJ](https://deepimagej.github.io) - [Key Reference](https://www.nature.com/articles/s41592-021-01262-9.epdf?sharing_token=gFbjdF-nflWTb11ulG7OwdRgN0jAjWel9jnR3ZoTv0NeCGAajxJJG9eNeKTuUDwD-rhKcp8lM5VPvscQ0aFZy_yWdNcPyVNt0r-ShB4cf_G0kZMRVgOoeQL6iHxScPIXcfKgBxgePB7jIMAk0K2zQk6TrnarJenPJemoyfnA4ts%3D)

![image](https://github.com/user-attachments/assets/851c0d53-8635-4cc2-bb01-7feb1e6cafad)

#### Gotcha Time
Although DeepImageJ plugins install under ImageJ running in a JetPack 5.1 L4T environment, a functional installation requires higher versions of Torch and ONNX than those integrated by default (at risk of breaking your NV environment).
Therefore, a JetPack 6.1+ installation should be required for using the DeepImageJ plugin to load and run available models.
