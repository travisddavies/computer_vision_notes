## Deep Learning II

## Understanding CNNs
- How do we know what a computer vision model is thinking?

![[understanding_cnns.png]]

## Learning Outcomes
- Explain methods that can be used to evaluate computer vision algorithms
- Explain strengths and weaknesses of CNNs for image recognition
- Evaluate computer vision algorithms in terms of their invariance (or tolerance) to image variation

# ImageNet Results

## What Affects Performance?

![[what_affects_performance.png]]

# Model Visualisation

## Feature Representation

![[feature_representation.png]]

## Visualising Features
- How are images organised in this feature space?
- It's a high-dimensional space, so we can't just plot all images in this space
	- Can use dimensionality reduction
	- Or look at local regions (what images are near neighbours in this space?)
- What do individual neuron responses represent?

## Nearest Neighbours

![[nearest_neighbours.png]]

## Feature Space Visualisation
- Options for dimensionality reduction
- PCA (principal component analysis)
	- Show the dimensions with the most variance
	- Simple but often hard to interpret since only a few dimensions can be visualised simultaneously
- t-SNE (t-distributed stochastic neighbour embedding)
	- Flatten high-dimensional data into 2D or 3D so that near neighbours stay nearby

![[cnnembed.png]]

## Visualising Convolutional Kernels
- Visualising the first convolutional layer kernels is easy because the input channels are RGB
- Visualising layer conv kernels is harder because the channels are high-dimensional and represent features

![[visualising_conv_kernels.png]]

## Maximally Activating Patches

![[maximally_activating_patches.png]]

- Run many images through the network and find patches that give the highest response in this channel:

![[maximally_activating_patches2.png]]

## Guided Backprop

![[maximally_activating_patches.png]]

- Compute gradient of neuron value with respect to image pixels
- Which pixels matter most for correct classification?

![[guided_backprop.png]]

## Visualising Convolutional Kernels

![[visualising_conv_kernels2.png]]

## Visualising Image Regions
- What parts of an image are most important for determining the class label?
	- Can help show what features the model uses to determine class
	- Can help debug problems (e.g., using background to classify objects, label confusion when there are multiple objects)
- Occlusion method: mask image and see how much class probability changes

![[visualising_image_regions.png]]

## CAM (Class Activation Mapping)
- Add a Global Average Pooling (GAP) layer before classification layer, use weights of this layer to determine _where_ the class-relevant features are

![[class_activation_map.png]]

![[class_activation_map2.png]]

- Disadvantage of CAM:
	- Most models don't use GAP, so the GAP layer must be added to a pretrained network and then finetuned
	- Only allows visualisation of the last layer
- More flexible alternative: Grad-CAM (Gradient Weighted Class Activation Mapping)

## Grad-CAM

![[grad-cam.png]]

- Take response from some layer $A \in \mathbb{R}^{H \times W \times K}$ 
- Compute gradient of class score with respect to layer response
- Global Average Pool (average over image $x$, $y$) the gradients to get a vector of weights $\alpha_k$ (1 weight per channel)
- Compute activation map _ReLU_ ($\sum_k \alpha_k A_{h,w,k}$)

![[grad_cam2.png]]

![[grad-cam3.png]]

## Visualising Classes
- Usually based on gradient ascent - synthesise image that maximises class label response
	- Initialise an image with zeros or small random noise
	- Run image through network, compute gradient
	- Update _image pixels_ in a direction that minimises loss
- Problem: there are many possible arrays of pixels that can generate very high model response; not all of these will look like realistic images

![[Computer Vision/Images/visualising_classes.png]]

![[visualising_classes2.png]]

![[visualising_classes3.png]]

# Invariance & Generalisation 

## Invariance / Tolerance
- Are the features learned by CNNs invariant to
	- Lighting?
	- Translation?
	- Image plane rotation?
	- Scale?
	- 3D rotation / pose?

![[invariance_tolerance_cnns.png]]

## Visualising Classes

![[visualising_classes_gougou.png]]

## Shape and Texture

![[shape_and_texture 1.png]]

![[shape_and_texture2.png]]

## Generalisation
- Models are very sensitive to some types of noise

![[generalisation_cnns.png]]

![[generalisation_of_cnns2.png]]

## Invariance / Tolerance
- CNNs are tolerant to variation included in training data
- But often not tolerant to variation that didn't appear in the training data
- Classification tends to rely on recognising a few key features / local texture elements

## Adversarial Images
- Adding small amounts of noise to an image can completely change the model's perception

![[adversarial_images_panda.png]]

## Generalisation
- Performance of top models on ImageNet vs. ImageNetV2
- Drop of about 10% suggests some overfitting to quirks of ImageNet

![[generalisation-of-cnns3.png]]

## Geographic Bias?

![[geographic_bias.png]]