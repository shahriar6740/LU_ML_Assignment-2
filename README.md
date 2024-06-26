# Neural Networks for Handwritten Digit Recognition | (Multiclass)
Neural networks are one of the most powerful mathematical strategies to mimic the function of the human brain to solve complex problems.

In this exercise, we will use a neural network to recognize ten handwritten digits, 0–9. This is a multiclass classification task where one of n choices is selected. Automated handwritten digit recognition is widely used today — from recognizing zip codes (postal codes) on mail envelopes to recognizing amounts written on bank checks.
# Dataset
The data set contains 5000 training examples of handwritten digits.

Each training example is a 20-pixel x 20-pixel grayscale image of the digit.

- Each pixel is represented by a floating-point number indicating the grayscale intensity at that location.
- The 20 by 20 grid of pixels is “unrolled” into a 400-dimensional vector.
- Each training example becomes a single row in our data matrix X.
- This gives us a 5000 x 400 matrix X where every row is a training example of a handwritten digit image.
The second part of the training set is a 5000 x 1-dimensional vector y that contains labels for the training set

- y = 0 if the image is of the digit 0, y = 4 if the image is of the digit 4 and so on.
This is a subset of the MNIST handwritten digit dataset (http://yann.lecun.com/exdb/mnist/)

# Softmax Function
A multiclass neural network generates N outputs. One output is selected as the predicted answer. In the output layer, a vector Z is generated by a linear function which is fed into a softmax function. The softmax function converts Z into a probability distribution as described below. After applying softmax, each output will be between 0 and 1 and the outputs will sum to 1. They can be interpreted as probabilities. The larger inputs to the softmax will correspond to larger output probabilities.

# Python Implementation
Required packages:

- NumPy is the fundamental package for scientific computing with Python.
- matplotlib is a popular library for plotting graphs in Python.
- tensorflow a popular platform for machine learning.
Import necessary packages
```python
import os
import numpy as np
import tensorflow as tf
from scipy.io import loadmat
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense
from tensorflow.keras.activations import linear, relu, sigmoid
import matplotlib.pyplot as plt
plt.style.use('./deeplearning.mplstyle')

import logging
logging.getLogger("tensorflow").setLevel(logging.ERROR)
tf.autograph.set_verbosity(0)
```
Implement the Softmax function:
```python
def my_softmax(z):  
    """ Softmax converts a vector of values to a probability distribution.
    Args:
      z (ndarray (N,))  : input data, N features
    Returns:
      a (ndarray (N,))  : softmax of z
    """
    e_z = np.exp(z - np.max(z))
    a = e_z / e_z.sum(axis=0) 
    return a
```
Load the Dataset from a .mat file
```python
data = loadmat(os.path.join('data', 'ex3data1.mat'))
X, y = data['X'], data["y"]

# set the zero digit to 0, rather than its mapped 10 in this dataset
y[y == 10] = 0
```
Check the shape of the data:
```python
print ('The shape of X is: ' + str(X.shape))
print ('The shape of y is: ' + str(y.shape))
```
# Vizualize the Data
We will be visualizing a subset of the training set.

- the following code randomly selects 64 rows from X, maps each row back to a 20 pixel by 20 pixel grayscale image and displays the images together.
- The label for each image is displayed above the image.
```python
m, n = X.shape

fig, axes = plt.subplots(8,8, figsize=(5,5))
fig.tight_layout(pad=0.13,rect=[0, 0.03, 1, 0.91]) #[left, bottom, right, top]

#fig.tight_layout(pad=0.5)
widgvis(fig)
for i,ax in enumerate(axes.flat):
    # Select random indices
    random_index = np.random.randint(m)
    
    # Select rows corresponding to the random indices and
    # reshape the image
    X_random_reshaped = X[random_index].reshape((20,20)).T
    
    # Display the image
    ax.imshow(X_random_reshaped, cmap='gray')
    
    # Display the label above the image
    ax.set_title(y[random_index])
    ax.set_axis_off()
    fig.suptitle("Label, image", fontsize=14)
```
# Model Representation and Tensorflow Implementation
The neural network we will use in this assignment is shown in the figure below.

- This has two dense layers with ReLU activations followed by an output layer with a linear activation.
- Recall that our inputs are pixel values of digit images.
- Since the images are of size 20×20, this gives us 400 inputs

The parameters have dimensions that are sized for a neural network with 25 units in layer 1, 15 units in layer 2 and 10 output units in layer 3, one for each digit.

- Recall that the dimensions of these parameters are determined as follows:
- If the network has 8_in units in a layer and 8_out units in the next layer, then
  - w will be of dimension 8_in × 8_out.
  - b will be a vector with 8_out elements
Therefore, the shapes of W, and b, are:

- layer1: The shape of W1 is (400, 25) and the shape of b1 is (25,)
- layer2: The shape of W2 is (25, 15) and the shape of b2 is: (15,)
- layer3: The shape of W3 is (15, 10) and the shape of b3 is: (10,)
  
The bias vector b could be represented as a 1-D (n,) or 2-D (n,1) array. Tensorflow utilizes a 1-D representation.

```python
tf.random.set_seed(1234) # for consistent results
model = Sequential(
    [               
        
        tf.keras.Input(shape=(400,)),
        Dense(25, activation='relu', name = "L1"),
        Dense(15, activation='relu',  name = "L2"), 
        Dense(10, activation='linear', name = "L3"),
       
    ], name = "my_model" 
)

```
The shapes of weights are:
```python
W1,b1 = layer1.get_weights()
W2,b2 = layer2.get_weights()
W3,b3 = layer3.get_weights()
print(f"W1 shape = {W1.shape}, b1 shape = {b1.shape}")
print(f"W2 shape = {W2.shape}, b2 shape = {b2.shape}")
print(f"W3 shape = {W3.shape}, b3 shape = {b3.shape}")
```
W1 shape = (400, 25), b1 shape = (25,) <br>
W2 shape = (25, 15), b2 shape = (15,)<br>
W3 shape = (15, 10), b3 shape = (10,) <br>

```python
model.compile(
    loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True),
    optimizer=tf.keras.optimizers.Adam(learning_rate=0.001),
)

history = model.fit(
    X,y,
    epochs=40
)
```
After 40 epochs the loss is: 0.0368

**A detail tutorial of this repository can be found at this [medium blog post](https://hasan-shahriar.medium.com/neural-networks-for-handwritten-digit-recognition-multiclass-474d5ab52d40)**
