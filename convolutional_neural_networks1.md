# Convolutional Neural Networks I
## Learning Outcomes
- Define image recognition as a computer vision problem
- Explain the differences between convolutional and fully-connected networks
- Implement convolutional layers

# Image Recognition

## What is Recognition?
- Below is an example of an image recognition task
- We could consider this image to be of a number of different classes, in this case: "dog", "shiba inu" and "doge"

![[shiba.png]]

## What is "Recognition"?
- In this section, we'll define **image recognition** as category-level recognition of the whole image
- Category-level=group level
	- Groups may be more or less specific ("bird", "duck", "Australian wood duck")
	- Different from **instance-level recognition**, recognising a specific individual
- Whole image = one label per image
	- Different from **detection** = locate object in image
	- Different from **segmentation** = label individual pixels

## Why is Recognition Difficult?
- Inter-category similarity

![[why_is_recognition_difficult.png]]

- Intra-category variability
	- Instances
	- Illumination
	- Scale
	- Viewpoint/pose
	- Background/occlusion

![[why_is_recognition_difficult2.png]]

## Goal of Image Recognition
Build a representation of the image that
1. Distinguishes different categories,
2. But is invariant (or tolerant) to variation within a category

## Visual Encoding

![[visual_encoding.png]]

## Visual Encoding - Early Vision

![[visual_encoding_early_vision.png]]

## Hand-Crafted Features

![[hand-crafted-features.png]]

## Deep Learning "Revolution"
- Neural networks have been used for computer vision problems since 1950s
- But they only become the state-of-the-art around 2010
- What changed?

![[deep-learning-revolution.png]]

## What Changed?
- Graphics processing units (GPUs) for fast parallel computation
	- CUDA (NVIDIA, 2007) - platform for parallel processing on GPU
- Algorithm improvements:
	 - Unsupervised pretraining, ReLU activation function, regularisation (e.g., dropout)
- The internet
	- Massive amounts of text, photos, etc. _with human-annotated labels_

## ImageNet
- Based on WordNet, a database of English words organised by concepts
- Class images collected online, manually cleaned by human annotators (2.5 years of annotation work)
- Over 5,000 classes, but commonly-used dataset includes just 1000 of the classes with most exemplars

![[imagenet.png]]

## Large-Scale Image Datasets

![[large-scale-image-datasets.png]]

## Image Recognition
- Supervised learning problem - map image to class label
- Pre-2010: small number of classes (order of 10-100), hand-crafted features
- 2010-now: "large scale" image recognition (order of 1000-10,000 classes), millions of images, deep-learned features

# Neural Network Review

## Neural Networks/MLP

![[mlp.png]]

## Neural Networks
- Multiple layers of neurons working in parallel on the same input
- Each neuron on layer $L$ receives input from all neurons on layer $L-1$ (**fully connected layer**) and produces one output
- Neuron's output is a weighted sum of the input, followed by a non-linear activation function

$$
y = f \Bigg(\Bigg[\sum_j \textcolor{red}{w_j} x_j\Bigg] + \textcolor{red}{b}\Bigg) = f(\textcolor{red}w \cdot x + \textcolor{red}b)
$$
- Characters in $\textcolor{red}{red}$ are weights and biases learned from data

## Non-Linear Activation Function?
- (logistic) sigmoid ($\sigma$):

![[logistic_sigmoid.png]]

- Hyperbolic tan (tanh):

![[hyperbolic_tan.png]]

- Rectified linear unit (ReLU):

![[relu.png]]

## Neural Networks
- Train through backpropagation (a form of gradient descent)
- Compute gradient of the loss function with respect to network parameters, starting with output layer and propagating to earlier layers, and adjust weights to reduce loss
- Learning rate is a free parameter
- Loss function usually based on difference between ground truth and prediction (supervised learning)

## Neural Networks
- Advantages
	- Universal approximator - able to approximate any continuous function on $\mathbb{R}^n$
	 - Feature embedding - learns complex features
	 - Parallelisable - within each layer, neurons are independent
- Disadvantages
	- Very large number of parameters - high memory/time/data requirements, prone to overfitting

# Convolutional Layers

## Convolutional Neural Network

![[conv_net.png]]

## Why Convolution?
- Regular neural networks can be used for image recognition
- But **convolutional** neural networks are more common for large images
- Why?
	- More efficient learning of local, repeated patterns
	- However, limits what the network can learn


![[why_convolution.png]]

## Convolution Layer

![[convolution_layerr.png]]

![[convolution_layer2.png]]

## Why Convolution?
- Regular neural networks can be used for image recognition
- But **convolutional** neural networks are more common for large images
- Why?
	- More efficient learning of local, repeated patterns
	- However, limits what the network can learn

## Convolutional Layer
- Convolutions are defined by
	- A **kernel**, which is a matrix overlaid on the image and computes an element-wise product with the image pixels
	- A **stride** which defines how many positions in the image to advance the kernel on each iteration (stride = 1 means the kernel will operate on every pixel of the image)

![[convolutional_layer2.png]]

![[convolutional_layer3.png]]

![[convolutional_layer4.png]]

![[convolutional_layer5.png]]

![[convolutional_layer6.png]]

![[convolutional_layer7.png]]

![[Computer Vision/Images/convolutional_layer8.png]]

![[convolutional_layer9.png]]

## Convolutional Layer Output

![[Computer Vision/Images/convolutional_layer_output.png]]

## Convolutional Layer Input/Output

![[convolutional_layer_output 1.png]]

![[convolutional_layer_output2.png]]

## Fully-Connected vs. Convolutional 
### Fully-Connected Layer
- Each neuron is connected to every neuron in the input
- The neuron learns some combination of the input
- The output to the next layer is the neuron's response

### Convolutional Layer
- Each neuron is connected to a small patch of the input
- The neuron learns a convolutional kernel on the input
- The output to the next layer is the input convolved with the neuron's kernel

## Fully-Connected vs. Convolutional

![[how_many_parameters.png]]

$n_{params} = h \times w \times n_{channels} + bias$

$n_{params} = 5 \times 5 \times 3 + 1 = 76$

## Convolutional Output Size
- Valid convolution (with kernel larger than 1x1) results in outputs smaller than input
- If same-size output is needed, pad the input (zero padding is most common)

## Convolutional Neural Network

![[conv-net-again.png]]

## Convolutional Layer Kernels
- What kernels do the convolutional layers learn?
- In layer 1, mostly edges
- In higher layers?

![[Computer Vision/Images/convolutional_kernel_layers.png]]

![[convolutional_kernel_layers2.png]]

![[convolutional_kernel_layers3.png]]

![[convolutional_kernel_layers5.png]]

## Convolutional Layers
- Advantages of convolutional layers
	- Efficient - learns to recognise the same features anywhere in the image, with fewer parameters compared to fully-connected layer
	- Preserves spatial relations - output is an image with values indicating where features are present
- Disadvantages of convolutional layers
	- Limited kernel size means model is limited to learning local features