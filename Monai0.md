## MONAI Zoo Testing 1 2 3...  
AGX Orin DK trains DenseNet with MedNIST data in 11+ m
![Screenshot from 2022-09-30 17-18-09](https://user-images.githubusercontent.com/71346897/193375578-34716c93-7bd8-4a4c-87c4-2976e406b65d.png)


Xeon workstation trains DenseNet with MedNIST data in 12+ m
![Screenshot from 2022-09-27 12-33-33](https://user-images.githubusercontent.com/71346897/193376165-4a6616a2-89cb-46b2-bb8f-fcc8371e511e.png)

Xeon workstation **locally** trains a 3D SegResNet model network with 2016/7 BraTS Brain Tumor Segmentation competition MRI data on RTX Titan/Turing GPU + CPU, 20 cores @ ~750 W; total training time ~ 82 hrs @ 91-100% GPU utilization; training step time ~2.3 sec
![image](https://user-images.githubusercontent.com/71346897/193698288-293ea78d-d9c5-4537-89a9-3dbfc594d165.png)

**After a complete Python system update and repair**, the same Xeon workstation trains a 3D SegResNet model network with 2016/7 BraTS data; total training time ~ 24.5 hrs @ 76-92% GPU utilization; training step time ~0.54 sec, ~3.5X increase in training speed
![image](https://user-images.githubusercontent.com/71346897/201801626-86735c02-d8c5-4ed7-9746-1d830c00e9b8.png)

