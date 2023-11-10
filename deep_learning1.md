# Deep Learning I

## Learning Outcomes
- Explain the key differences between commonly-used architectures for ImageNet classification
- Evaluate image classification results
- Explain and implement transfer learning with networks pretrained on ImageNet

# Common Architecture

## AlexNet

![[alexnet.png]]

## AlexNet Innovations
- ReLU (Rectified Linear Unit) activation function - faster training
- Training on GPU - parallelisation allows faster training (actually required 2 GPUs at the time!)
- Overlapping max pooling regions, response normalisation after ReLU - small accuracy increase
- Data augmentation - reduces overfitting
- Dropout - reduces overfitting

## VGG

![[vgg.png]]

Other VGG variations with more/fewer layers (e.g., VGG-19)

## Stacked Convolutional Layers
- VGG stacks multiple 3x3 convolutional kernels to effectively make larger kernels:
	- Two 3x3 conv. layers = effective receptive field of 5x5
	- Three 3x3 conv. layers = effective receptive field of 7x7

![[stacked_convolutional_layers.png]]

## VGG Innovations
- Stacked 3x3 convolutional layers
	- Learn more complex features thanks to additional non-linearities
	- Fewer parameters than 1 layer with the equivalent receptive field
- Doesn't use AlexNet's response normalisation - allows faster training with only very small accuracy to drop

## GoogLeNet (Inception)

![[googlenet.png]]

## Inception Module
- Choosing the right kernel size in CNNs is difficult because objects/features can appear at any scale
- Solution: use multiple kernel sizes and concatenate

![[inception_module.png]]

- 1x1 convolutional layers reduce the number of channels (dimensionality reduction)

![[inception_module2.png]]

## GoogLeNet Innovations
- Inception module
	- Learns features at a variety of kernel sizes/scales
- Auxillary classifiers
	- Used during training only - classify images based on early layer representations and update parameters
	- Helps with vanishing gradient problem

## ResNet: Background
- Will deeper neural networks always give better performance?

![[Computer Vision/Images/resnet.png]]

## ResNet: Background
- No, performance saturates and then decreases
- Not due to overfitting - performance is worse on the training set

![[resnet_background.png]]

- It should be possible to learn parameters in the deep network that would allow it to act like the small network
	- For example, some conv. layers learn identity kernels, while others learn the shallow network's kernels
- However, deep CNNs cannot learn this solution (at least, not within a reasonable training time)

## ResNet
- Solution: Add "shortcut connections" that skip some layers

![[resnet2.png]]

## Residual Learning
- Reformulate the learning problem
	- Traditional network: input $x$, output $H(x)$, which is the feature representation of $x$
	- Residual network: input $x$, learn $H(x) - x$, which is then added to $x$ to get $H(x)$
- Makes it easier to learn identity mapping

![[residual_learning.png]]

## ResNet Innovations
- Residual Block
	- Simplifies the learning problem by making it easier for networks to learn identity mapping
	- Allows deeper networks to improve accuracy

![[resnet_innovations.png]]

## MobileNets
- Lightweight architectures for mobile apps

![[mobilenets.png]]

- Separable filters
	- Recall that filtering with a 2D filter is equivalent to filtering with two orthogonal 1D filters
	- Similarly, filtering with a 3D filter is equivalent to filtering with a 2D filter and an orthogonal 1D filter
- MobileNets uses depthwise-separable filters - 2D filters in $x$, $y$ and 1D filters over channels

![[mobilenet_architecture.png]]

## MobileNets Innovations
- Depthwise separable convolution
	- Fewer parameters and less computation
	- Limits what kernels the model can learn - not all kernels are separable
- Smaller and faster than other architectures
	- Lower accuracy than VGG, ResNet, etc
	- But better suited for real-time applications, phones

# Classification Results

## ImageNet Classification
- 1000 object classes
- Model output = a probability distribution (from softmax) over 1000 class labels
- Top-1 accuracy
	- For each test image, model is correct if the most likely class == ground truth class
- Top-5 accuracy
	- For each test image, model is correct if any of the 5 most likely classes == ground truth class

## Classification Performance

![[classification_performance.png]]

## Classification Performance

![[classification_performance2.png]]

## Common Architectures

![[common_architectures.png]]

## Classification Errors

![[classification_errors.png]]

## Generalisation
- Features from neural networks are good representations for a range of tasks

![[generalisation.png]]

# Transfer Learning

## Image Recognition: Pixels

![[image_recognition_pixels.png]]

- Pixels are a poor space for classification
	- High-dimensional space: 256x256x3 image = 196,608 attributes
	- Irrelevant transformations (translation, lighting change, scale change, rotation, etc.) cause large changes in pixel values

## Image Recognition: Features

![[image_recognition_features.png]]

## Image Recognition: Features
- A good feature space for image recognition:
	- Is lower-dimensional - e.g., 1000s of values per image
	- Projects from the same class into a similar part of the space (images with the same class label have similar features)

## Using Pretrained Networks
- CNNs convert images from pixels to high-level features that are good for classification (feature embedding)
- These high-level features give good performance on a range of computer vision tasks
- Transfer learning - use features from a CNN trained on a large-scale task (e.g., ImageNet classification as input for another task, with minimal retraining)

## Transfer Learning
- **Embedding** of an input = the network's response to the input at some layer

![[alexnet2.png]]

- Extract the representation from a late layer of a CNN trained on ImageNet
	- E.g., for each image take the activations from the 4096 neurons that feed into the 1000-way ImageNet classification
- Use the neurons' activations as the attributes for a classifier of your choice (e.g., SVM, K-NN, etc.)

![[transfer_learning_pipeline.png]]

![[transfer_learning_last_layer.png]]

![[transfer_learning_swap.png]]

![[transfer_learning_initialise.png]]

![[transfer_learning_freeze.png]]

## Retraining Layers
- **Finetuning** = retraining layers of a pretrained CNN
- How many layers to fine tune depends on dataset size and how similar it is to ImageNet
	- More dissimilar datasets may need more retraining of lower layers
	- If dataset size is limited, training lower layers may just lead to overfitting

## Example: Pretrained Features

![[pretrained_features.png]]