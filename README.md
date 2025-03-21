# Machine Learning Methods - Experimenting with MLPs & CNNs

## Project Summary

In this project, I experimented with **Multi-Layer Perceptrons (MLPs)** and **Convolutional Neural Networks (CNNs)**. The study begins with MLPs applied to a dataset of European countries and later transitions to **image classification** using CNNs to detect deepfake-generated images.

---

## Multi-Layer Perceptrons (MLPs)

### Dataset
The dataset consists of tables with three columns:
- **Longitude**
- **Latitude**
- **Country** (encoded as an integer)

The goal is to classify a city into its corresponding country based on geographical coordinates.

### Optimizing MLP Training  
_Code implementation: `NN_tutorial.py`_

#### Learning Rate
A high learning rate can lead to **oscillations in loss**, making training unstable. Conversely, a lower learning rate ensures **more stable learning**. The figure below demonstrates these effects:

<p align="center">
  <img src="https://github.com/user-attachments/assets/b8678682-be5e-4385-abb5-5169a5f19325" alt="Learning Rate Effects" width="500"/>
</p>

#### Epochs
Too few epochs prevent the model from learning patterns effectively, leading to high losses. Too many epochs cause **overfitting**, observed around the **80-epoch mark**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/d28e0919-9a3f-426b-8c71-c9ad2011d8bc" alt="Epochs Effect" width="500"/>
</p>

#### Batch Normalization
Batch normalization stabilizes training, leading to faster convergence compared to models without it.

<p align="center">
  <img src="https://github.com/user-attachments/assets/7041ce4d-e983-4346-bcad-267e8ac8cf3c" alt="Batch Normalization" width="500"/>
</p>

#### Batch Size
- **Larger batch sizes** achieve **higher test accuracy**.
- **Smaller batch sizes** result in **slower training speeds per epoch**.
- **Stability**: Larger batch sizes show **smoother loss curves** compared to smaller ones.

<p align="center">
  <img src="https://github.com/user-attachments/assets/05a82609-6e3a-4863-a9f0-85d826543668" alt="Batch Size Effect" width="500"/>
</p>

### Evaluating MLP Performance  
_Code implementation: `main.py`_

In this part of the project, I trained 8 classifiers for the following depths and widths {(1,16), (2, 16), (6, 16), (10, 16), (6, 8), (6, 32), (6, 64)} and optimized each model by tweaking the parameters.

- **Best model:** Achieved **validation accuracy ≈ 0.75**, with training, validation, and test losses stabilizing around **0.25**.

<p align="center">
  <img alt="Light" src="https://github.com/user-attachments/assets/bb060533-db8f-492b-a617-07506ae994c0" width="45%">
&nbsp; &nbsp; &nbsp; &nbsp;
  <img alt="Dark" src="https://github.com/user-attachments/assets/1d846194-49e8-49b8-9c25-b1b30be0f142" width="45%">
</p>

- **Worst model:** Showed validation accuracy **≈ 0.50**, with higher loss convergence (**≈ 1.0**).

<p align="center">
  <img alt="Light" src="https://github.com/user-attachments/assets/380156a6-c3bf-4754-88bf-4b704996de17" width="45%">
&nbsp; &nbsp; &nbsp; &nbsp;
  <img alt="Dark" src="https://github.com/user-attachments/assets/20d2207b-c43c-4ee1-a1eb-fc368a9b6a59" width="45%">
</p>

#### Depth vs. Accuracy
- **Optimal depth:** **2 hidden layers**
- More layers lead to **diminishing returns** due to the **vanishing gradient problem**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b49e8f66-88f8-40f0-b9a2-74ef0b959fc1" alt="Depth vs Accuracy" width="500"/>
</p>

#### Width vs. Accuracy
- **Optimal width:** **30 neurons per hidden layer**
- Too few or too many neurons reduce accuracy.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b5ab32ee-e76a-476f-b803-ce928a994fe9" alt="Width vs Accuracy" width="500"/>
</p>

#### Monitoring Gradients
- Without batch normalization: **Vanishing gradients** in the early layers.

<p align="center">
  <img src="https://github.com/user-attachments/assets/f9ae681b-96ea-441d-8dd8-775fbe09811c" alt="Gradient vs Epoch" width="500"/>
</p>

- With batch normalization: **Gradients explode**, requiring careful tuning.

<p align="center">
  <img src="https://github.com/user-attachments/assets/40b863ad-787b-4b29-91ea-f2d2f0945382" alt="Gradient vs Epoch with BatchNorm" width="500"/>
</p>

#### Implicit Representation
When applying deep learning on some low dimensional data with a sequential pattern (e.g. time, location, etc.) it is common to first create an implicit representation of the data. This simply means passing the data through a series of sines and cosines. Sines and cosines are functions which may take a NN several layers to implement by itself, but are very useful. By doing this process ourselves we can leave the network to focus on more complex patterns which we can’t recognize and wish it to learn by itself.
I implemented an implicit representation pre-processing to the data, passing the input through 10 sine functions and trained an NN with 6 and width 16 on top of these representations.
Transforming input coordinates using **sine and cosine functions** improves decision boundaries, allowing the model to learn more complex patterns.

<p align="center">
  <img alt="Light" src="https://github.com/user-attachments/assets/c48859d0-0978-452f-8f75-8d8ab0fc5500" width="45%">
&nbsp; &nbsp; &nbsp; &nbsp;
  <img alt="Dark" src="https://github.com/user-attachments/assets/9ffa6017-fa1f-4b30-acc3-af1958bb93ae" width="45%">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/37526961-0607-452a-8709-6afd1d745644" alt="Decision Boundaries" width="500"/>
</p>

---

## Convolutional Neural Networks (CNNs)
_Code implementation: `cnn.py`_

### Task: Deepfake Image Classification
Using CNNs, I tackled **binary classification** to distinguish real human faces from deepfake-generated images. The dataset is available [here](https://drive.google.com/file/d/1KBT7gpeo2fDj8-J_pc3dFWu4LLEDwKKp/view).

### Model Comparisons

#### XGBoost (Baseline)
_Code implementation: `xg.py`_
- **Accuracy:** 73.5%

#### Training from Scratch (ResNet18)
- **Epoch 1 Loss:** 0.7758
- **Validation Accuracy:** 52.5%
- **Test Accuracy:** 52.25%
- **Learning Rate:** 0.01

#### Linear Probing (Pretrained ResNet18)
- **Epoch 1 Loss:** 0.6795
- **Validation Accuracy:** 69.5%
- **Test Accuracy:** 72.5%
- **Learning Rate:** 0.01

#### Fine-Tuning (Pretrained ResNet18)
- **Epoch 1 Loss:** 0.6244
- **Validation Accuracy:** 74.0%
- **Test Accuracy:** 77.5%
- **Learning Rate:** 0.001

### Best vs. Worst Model Comparison
- **Best model:** **Fine-tuned ResNet18** (Test accuracy = **77.5%**)
- **Worst model:** **Training from scratch** (Test accuracy = **52.25%**)

### Sample Analysis
_Code implementation: `five_samples.py`_
Five images correctly classified by the **Fine-tuned model** but misclassified by the **Training from scratch model**:

<p align="center">
  <img alt="Light" src="https://github.com/user-attachments/assets/a176fafc-4c5e-402b-9a44-95475813a708" width="45%">
&nbsp; &nbsp; &nbsp; &nbsp;
  <img alt="Dark" src="https://github.com/user-attachments/assets/6d431eea-df77-415b-8331-b6e300c04b15" width="45%">
</p>

<p align="center">
  <img alt="Light" src="https://github.com/user-attachments/assets/0420a65d-6c69-4e3d-bca4-d493f955fab0" width="45%">
&nbsp; &nbsp; &nbsp; &nbsp;
  <img alt="Dark" src="https://github.com/user-attachments/assets/439252e7-ed50-46e5-9458-2f8a8e73c617" width="45%">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/771f320b-4d91-4d1a-807c-5e68dfde8f35" width="500"/>
</p>

---

## Results & Insights

### Key Findings
- **MLPs work well for structured tabular data**, but require careful tuning of depth, width, and batch normalization.
- **CNNs are highly effective for image classification**, especially with **transfer learning** (e.g., **fine-tuning ResNet18**).
- **Fine-tuning a pretrained model significantly outperforms training from scratch**.
- **Implicit representation techniques enhance MLPs' ability to capture complex patterns.**

**Full report can be found in the ML_Methods_Ex4_Report.pdf**

---

